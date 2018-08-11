# wepy-develop-note
记录使用wepy开发过程中一些注意事项和解决办法

## app.wpy 中无法直接调用 store/action

因为 app.wpy 中没有使用@connect, 所以无法通过调用action来触发reducer, 项目中存在情况为:
在page初始化之前的app过程中, 读取缓存的sessionId, 通过api获取用户信息后保存到store/user中.
解决办法:
```javascript
import { setStore, getStore } from 'wepy-redux'
import { getUserInfoBySession } from './api'
```
```javascript
  getStoredUserSession () {
    try {
      let userSession = wx.getStorageSync('userSession')
      if (userSession) {
        // 存在用户session, 使用session获取用户信息
        getUserInfoBySession(userSession).then(succ => {
          const store = getStore()
          store.dispatch({ type: 'UPDATE_USERINFO', payload: succ })
        })
      } else {
        // 不存在用户session
        console.log('no user session exist.')
      }
    } catch (e) {
      // 出现错误
      console.log(e)
    }
  }
```
``` javascript
  onLaunch() {
    this.getStoredUserSession()
  }
```
(注意: 此处因为在app初始化时通过wepy-redux执行了setStore(store), 所以通过getStore()来获取出来的store是与app绑定后的store, 直接使用store也许会出现未知问题)
