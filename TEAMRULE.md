### Why 🤔
   一部分是学习来自于开源社区巨佬们，另一部分是对自己待过的四个厂百度、支付宝、字节、腾讯，自己觉得最舒服的 TEAMWORK 和 Code Style 沉淀

### TEAM WORK
 - ESlint Prettier 与 VScode Workspace<br>
这里是在腾讯的时候，发现的一个工作流问题，有一些 Pipe Line 会阻止 warning 的lint error，但是我本地没有 Eslint，只有 Prettier，导致无法在开发阶段去发现并且自动 Ctrl+S 去修复<br>
最佳实践：ESlint 在开发阶段自动读取 WorkSpace 中的配置并且 Ctrl+S 会自动去 fix 这些 warning<br>
No Prettier ❌
```json
// .vscode/settings.json
{
  "eslint.validate": [
    "javascript",
    "typescript",
    "javascriptreact",
    "typescriptreact",
    "vue",
    "json",
    "jsonc",
    "json5"
  ],
  "stylelint.validate": [
    "css",
    "less",
    "scss"
  ],
  "editor.codeActionsOnSave": {
    "source.fixAll": true
  }
}

```
### Code Style
#### Hooks<br>
- 与 React 原生 Hook 写法保持一致 小驼峰，返回 State 与 UpdateState
```tsx
    const useCpData = () => {
      ...
      return [cpData, setCpData]
    }
```

  -  Hook 与 Modal State
在一些情况下 在 Hook 中处理 Modal 中的状态去复用极为方便
举个例子 有大大小小 10 个模块，且可以单独通过 url 进入，此时 Modal 中不一定存在对应的 State Value,但每个页面都需要这个 Modal State<br>
> 优点:
>    - 只负责 Save Init State Or Get State from Modal，脱离业务逻辑
>    - 逻辑复用极其简单，无需在每个模块去 Init State
```tsx
    const useUserInfo = (props) => {
      const userInfo = useRedux(state: GlobalModal => state.Modal.userInfo)
      const setUserInfo = useRedux(state: GlobalModal => state.Modal.setUserInfo)

      const fetchUserInfo = () => {
          dispatch(params) // saveState to Modal
       }

      useEffect(() => {
      if(!userInfo){
        fetchUserInfo()
        }
      },[])
      ...
      return [userInfo, setUserInfo]
    }
```
  - 功能型 Hook 不与业务数据相关联，请提取出 @TEAM/utils 第三方 PKG
    如 useEventOutSide 这种
#### Component<br>
  - 组件命名 - 大驼峰 文件名：大驼峰 or 连接符
  - 业务组件 - 推荐 Data(数据) 与 View(视图) 分离,用相应的 Data Hook 去 管理/创建 CP 单例<br>
    举个例子，这里有一个商业化复杂应用场景下的 Table Modal, 虽然复杂 但是极其耐用 <br> 
    具体的功能大概是：<br>
    复杂业务场景的 Search + 后端分页 + 复杂业务表格数据以及嵌套多重数据 + 自定义 UI 插槽 + 以及各种 Modal Render Props
```tsx
  // 通用组件 非 Page CP
  // components/TableModal/index.tsx
  interface TableModalProps{
    records: TableRecords;
    columns: TableColumns;
    pagination: PaginationInfo;
    searchProps: TeamSearchCpProps,
    ...
  }

  const TableModal:FC = () => {
    return <> ... </>
  }

  export default TableModal;
  // components/TableModal/hooks/useTableModalData.ts
  const useTableModalData = () => {
    const [data, setData] = useState<TableModalData>([])
    const [pagination, setPagination] = useState<PaginationInfo>({ pageIndex: 1, pageSize });
    const [tableStatus, setTableStatus] = useState<TableStatus>(TableStatus.empty)
    ... Hook with Data 
    return [data, pagination, tableStatus, setData, setPagination, setTableStatus]
  }
```
    
