### 方式A

#### 页面组件

- 在 `/src` 下新建 `views` 目录
- 在文件夹下建立个页面组件的目录，并且建立 `index.js`，用于编写页面组件
- 在 `views` 目录下新建 `index.js` 文件，用于统一导出各页面组件
- 若要写 css 样式，则在对应组件目录下直接编写，命名方式也可为 `index.less`

-----

#### 通用组件

与页面组件管理方式一样：

- 在 `/src` 下新建 `components` 目录
- 在文件夹下建立个组件的目录，并且建立 `index.js`，用于编写组件
- 在 `components` 目录下新建 `index.js` 文件，用于统一导出各组件
- 若要写 css 样式，则在对应组件目录下直接编写，命名可为 `index.less`

----

#### redux

- 在 `/src` 下建立 `store` 目录，在该目录下新建一个 `index.js` 文件，用于创建 redux 和引入中间件

- 在 `store` 目录下根据功能创建各功能目录，在该目录内创建功能涉及到的 action 和 reducer 和 types

这样就能很明确的指定哪些功能有哪些 action 和 会触发哪些 reducer

不按组件划分的原因可能会有很多组件用到了同样的 reducer 和 action 功能，他们很多时候不只是服务于一个组件的，用组件的关系划分可能会存在很多重复

```
具体该如何分开这些功能则按具体项目要求决定，如一个小型网站只有用户和用户操作的功能
则可以分为两块，如 user 和 doing 目录
user目录：负责用户的登入注册登出等这些操作所需要的reducer和action功能
doing目录：负责用户的留言点赞转发等这些操作所需要的reducer和action功能
```

**目录结构**

```
src
 |	
 |__ store
      |
      |__ user
      |    |__ actions.js
      |    |__ reducer.js
      |    |__ types.js
      |
      |__ index.js
```

**如果使用到了 redux-saga 中间件：**

- 在 `src` 下新建 `saga.js` 文件。。。。
- 在各redux各功能目录下，新建 `saga.js` 文件，用于创建各功能的saga

-----

#### 公共功能

在 `src` 下新建一个 `common` 目录，用于导出公共的功能，如配置请求的后端地址，通用的枚举，通用的接口等

可以分别在 `common` 新建 `config` 、 `enum` 、`interface` 等目录并下下面建立 `index.js` 用于写入对应的代码并导出











