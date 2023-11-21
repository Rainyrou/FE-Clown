#### 头像

`avatar.vue` 是一个头像组件，其主要功能是展示用户头像，点击头像跳转到相应页面

###### 封装思路

复用 + 定制 + 交互 + 封装

###### Props

- avatarUrl + defaultUrl
- avatarShape + avatarSize
- avatarIsCreator + avatarUserId + avatarIdentityId

###### Data

- `imgStyle`：定义了图片宽高

###### Methods

- `goToHisPage` 方法用于处理点击头像时的导航逻辑，根据 `avatarIsCreator`、`avatarUserId` 和 `avatarIdentityId` 的值来决定跳转到哪个页面

#### ImageCache

**性能优化 & 用户体验 & 业务逻辑 & 封装思路**

`ImageCache` 组件实现了图片缓存机制，在多次加载同一图片时，图片可以从本地缓存中获取，而不是每次都从网络请求

###### 业务逻辑

1. **图片缓存处理**：当组件接收到一个新的 `src`（图片 URL）时，它会检查本地是否已缓存了该图片。如果已缓存，直接使用本地路径加载图片；如果未缓存，则都从网络请求并触发缓存
2. **事件传递**：组件允许父组件监听图片 `load`、`error` 和 `click` 事件，使父组件据此执行相应操作
3. **图片加载优化**：组件支持懒加载和图片显示动画效果

###### 封装思路

1. **可配置性**：通过 `props` 提供了多种配置项，如 `lazyLoad`（懒加载）、`fadeShow`（淡入效果）、`webp`（webp 格式支持）等，使组件更灵活，能适应不同的使用场景
2. **分离逻辑**：缓存逻辑被封装在 `imgCache.js` 中，这样 `ImageCache` 组件只关注于如何使用这些逻辑，而不是如何实现它们，这使得维护和测试更加容易
3. **性能优化**：通过图片缓存减少了网络请求的次数，加快了图片的加载速度，尤其在慢速网络环境下

###### 性能优化和用户体验

1. **本地缓存机制**：减少了网络请求，加快了图片加载速度
2. **懒加载**：图片仅在需要显示时才加载，减少了页面初始加载时间，提高了页面性能
3. **动画效果**：通过 `fadeShow` 属性为图片加载添加了动画效果，提升了用户体验
4. **事件监听**：允许父组件对图片的加载情况进行响应，例如在图片加载失败时显示占位图
5. **MD5 文件名**：使用 MD5 算法生成唯一的文件名，避免缓存冲突，确保文件的唯一性

###### Props

- `src`：图片的网络 URL
- `mode`：定义图片如何适应容器框，比如 `aspectFill`
- `lazyLoad`：决定是否懒加载图片
- `fadeShow`：图片加载时是否有淡入效果
- `webp`：是否支持 webp 格式
- `showMenuByLongpress`：长按图片时是否显示菜单
- `draggable`：图片是否可拖动
- `style`：自定义样式

###### Data

- `cacheImgUrl`：用于存储本地缓存的图片 URL

###### Watchers

- 监听 `src` 的变化，并调用 `handleCache` 方法处理图片缓存

###### Methods

- `handleCache`：检查传入的图片 URL 是否已被缓存。如果已缓存，它会更新 `cacheImgUrl` 为本地 URL。如果未缓存，它会保留网络 URL 并触发缓存过程
- `sendEvent`：用于向父组件发送 `load`、`error` 和 `click` 等事件

###### 工具类




