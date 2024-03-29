`key` 为每个节点提供一个唯一标识，让 Diff 算法在更新过程中准确识别和追踪每个节点，尽可能的复用现有节点。当用 `v-for` 更新已渲染过的元素列表时，它默认使用"就地更新"的策略，这一策略有助于高效优化现有节点的复用和重排

- 避免使用 `index` 作为 `key`。首先，使用 `index` 作为 `key` 和完全不用 `key` 的效果没什么两样。其次当元素列表顺序变化时，`key` 作为节点的唯一标识，它是不变的，这会让 Vue 误以为节点没有变化，错误地复用和更新节点，导致性能问题和意外行为
- 避免使用随机数作为 `key`。使用随机数作为 `key` 会导致 Vue 在每次渲染时都无法追踪节点，因为每次得到的 `key` 完全不同，Vue 误认为所有节点都是新的，导致其删除所有旧节点并重新创建新节点，增加不必要的渲染和性能开销
- 使用唯一 ID 作为 `key`。当用 `v-for` 渲染列表时，应使用每个列表项的唯一标识如由后端返回的 ID 作为 `key`，这样可以确保即使列表项的顺序发生变化，Vue 也能准确追踪每个节点，尽可能的复用现有节点，减少不必要的 DOM 操作
