```javascript
1、在需要的页面直接引入<elastic-drawer :list="list" :bgWallHeight="500" bgUrl="背景墙图片地址"></elastic-drawer>
2、提供抽屉里的上半部分内容区域具名插槽、使用方法参考示例工程
3、list为滚动列表的数据
4、目前已支持vue/nvue
```

|      参数解释      |        键名        |       类型      |  默认  |
| :------------: | :--------------: | :-----------: | :--: |
|     滚动列表数据     |     **list**     |     Array     |  \[] |
|      背景墙高度     | **bgWallHeight** | String/Number |  250 |
| 是否显示抽屉里的上半部分内容 |  **isShowCard**  |    Boolean    | true |
|    设置抽屉圆角尺寸    |  **radiusSize**  | String/Number |  15  |
|   是否显示tabs选项卡  | **isShowSticky** |    Boolean    | true |
|     背景墙图片地址    |     **bgUrl**    |     String    |  ""  |
|    是否显示抽屉列表    |  **isShowList**  |    Boolean    | true |

