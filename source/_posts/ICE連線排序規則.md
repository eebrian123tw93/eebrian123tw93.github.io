---
title: ICE連線排序規則
categories: []
date: '2024-12-24 15:02:00'
updated: '2025-02-19 08:51:03'
description: ''
abbrlink: f38f09a7
tags:
  - WebRTC
keywords:
  - WebRTC
---
# Candiates Examples
```sdp
// 组标识为 udpcandidate，使用 RTP 协议传输数据，基于 UDP 协议，
// 优先级为 1076558079，地址为 172.217.27.142:31269，类型为 host；
// host 表示客户端与该 candidate 位于同一个 LAN 或者 NAT，因此可以直连
a=candidate:udpcandidate 1 udp 1076558079 172.217.27.142 31269 typ host

// 组标识为 tcpcandidate，使用 RTP 协议传输数据，基于 TCP 协议，
// 优先级为 1076302079，地址为 172.217.27.142:31368，类型为 host；
// passive（被动）这个概念仅属于 TCP candidates，
// 表示该 candidate 不会主动发起连接，只能被动等待连接
a=candidate:tcpcandidate 1 tcp 1076302079 172.217.27.142 31368 typ host tcptype passive
```
# 候选对（Candidate Pair）
### **候选列表**

在 WebRTC 的 ICE 流程中，通信双方都会生成各自的 **候选列表**：

1. **本地候选列表**：由本地网络接口发现的候选（包括 Host、Server Reflexive、Relayed 等）。
 <!-- more -->
 2. **远程候选列表**：从对等方通过信令协议（如 SDP）传递过来的候选列表。
### **候选对的定义**
候选对是本地候选和远程候选的组合。  
假设：
- 本地候选列表有 3 个候选：`A1`, `A2`, `A3`。
- 远程候选列表有 2 个候选：`B1`, `B2`。
候选对的组合方式是**笛卡尔积**，即将每个本地候选与每个远程候选配对：
- 候选对 = {`(A1, B1)`, `(A1, B2)`, `(A2, B1)`, `(A2, B2)`, `(A3, B1)`, `(A3, B2)`}
### **候选对的用途**
候选对是用于连接性检查的单位。ICE 通过尝试不同的候选对来找出最佳的连接路径。
1. **连接测试**：对于每个候选对，ICE 会发送 STUN 请求来检查它们是否能够成功建立连接。
    - 如果候选对能通过 STUN 验证，它会被标记为 "有效候选对"。
    - 最终选择一个优先级最高的有效候选对作为实际的连接路径。
2. **打洞（NAT Traversal）**：候选对的连接性检查也会触发 NAT 打洞，帮助双方建立直接连接。
# 拉程式碼參考{% post_link  "編譯程式碼webrtc" %}
# p2p/base/basic_ice_controller.cc --> SortAndSwitchConnection
```c++
IceControllerInterface::SwitchResult
BasicIceController::SortAndSwitchConnection(IceSwitchReason reason) {
  // Find the best alternative connection by sorting.  It is important to note
  // that amongst equal preference, writable connections, this will choose the
  // one whose estimated latency is lowest.  So it is the only one that we
  // need to consider switching to.
  // TODO(honghaiz): Don't sort;  Just use std::max_element in the right places.
  absl::c_stable_sort(
      connections_, [this](const Connection* a, const Connection* b) {
        int cmp = CompareConnections(a, b, std::nullopt, nullptr);
        if (cmp != 0) {
          return cmp > 0;
        }
        // Otherwise, sort based on latency estimate.
        return a->rtt() < b->rtt();
      });

  RTC_LOG(LS_VERBOSE) << "Sorting " << connections_.size()
                      << " available connections due to: "
                      << IceSwitchReasonToString(reason);
  for (size_t i = 0; i < connections_.size(); ++i) {
    RTC_LOG(LS_VERBOSE) << connections_[i]->ToString();
  }

  const Connection* top_connection =
      (!connections_.empty()) ? connections_[0] : nullptr;

  return ShouldSwitchConnection(reason, top_connection);
}
```

c_stable_sort 將 connections_ 做排序 ，排序完後拿出第一個 connection 在呼叫 ShouldSwitchConnection 
c_stable_sort 裡面使用類似lambda用法
``` C++
int cmp = CompareConnections(a, b, std::nullopt, nullptr);
if (cmp != 0) {
  return cmp > 0;
}
// Otherwise, sort based on latency estimate.
return a->rtt() < b->rtt();
```
先去 CompareConnections 排序
如果沒有比較出來就是用 rtt <同一個封包來回時間（Round-Trip Time）> 做比較
## CompareConnections
```c++
int BasicIceController::CompareConnections(
    const Connection* a,
    const Connection* b,
    std::optional<int64_t> receiving_unchanged_threshold,
    bool* missed_receiving_unchanged_threshold) const {
  RTC_CHECK(a != nullptr);
  RTC_CHECK(b != nullptr);

  // We prefer to switch to a writable and receiving connection over a
  // non-writable or non-receiving connection, even if the latter has
  // been nominated by the controlling side.
  int state_cmp = CompareConnectionStates(a, b, receiving_unchanged_threshold,
                                          missed_receiving_unchanged_threshold);
  if (state_cmp != 0) {
    return state_cmp;
  }

  if (ice_role_func_() == ICEROLE_CONTROLLED) {
    // Compare the connections based on the nomination states and the last data
    // received time if this is on the controlled side.
    if (a->remote_nomination() > b->remote_nomination()) {
      return a_is_better;
    }
    if (a->remote_nomination() < b->remote_nomination()) {
      return b_is_better;
    }

    if (a->last_data_received() > b->last_data_received()) {
      return a_is_better;
    }
    if (a->last_data_received() < b->last_data_received()) {
      return b_is_better;
    }
  }

  // Compare the network cost and priority.
  return CompareConnectionCandidates(a, b);
}
```

1. [CompareConnectionStates 根據狀態](#CompareConnectionStates-根據狀態)
2. remote_nomination (如是受控端)推薦值
3. last_data_received (如是受控端)上次接收數據時間戳
4. [CompareConnectionCandidates 網路環境](#CompareConnectionCandidates-網路環境)

### CompareConnectionStates 根據狀態
1. 是否可寫
2. 是否可收
3. 是否連接
### CompareConnectionCandidates 網路環境
1. [優先級](#優先級)
2. 更新代數
3. 是否被修剪
#### 優先級
WebRTC 会创建一个候选对（Candidate Pair）的检查列表，用于连接性检查。候选对的排序遵循以下规则：
##### 候选对优先级计算
候选对优先级=2^32×min(本地优先级,远程优先级)+2×max(本地优先级,远程优先级)+(本地优先级>远程优先级?1:0)
參考p2p/base/connection.cc  Connection::priority()
# 結論

比較先後順序
1. States 狀態
	1. 是否可寫
	2. 是否可收
	3. 是否連接
2. remote_nomination (如是受控端)推薦值
3. last_data_received (如是受控端)上次接收數據時間戳
4. Candidates 網路環境
	1. 網路類型 
	2. 優先級
	3. 更新代數
	4. 是否被修剪
5. rtt 來回時間
# 參考
- [webrtc ICE穿透连接排序算法详解](https://blog.csdn.net/breezeewu/article/details/142210879)
- [ICE 连接排序规则](https://webrtc.mthli.com/connection/ice-connection-sorting/#compareconnectionstates)
- 
