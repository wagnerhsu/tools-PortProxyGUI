# README

- 程序模拟`netsh interface portproxy`。程序不是直接调用命令而是操作注册表
  - 注册表位置`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IPRIP\Parameters\PortProxy`
  - netsh命令并非直接修改内存中的网络栈配置，而是作为一个配置接口，将端口转发规则写入特定的注册表键值中。Windows 的 IP Helper 服务（iphlpsvc）会读取这些注册表项来应用实际的转发逻辑

## 技术细节

- 服务依赖: 修改注册表后，必须确保 IP Helper (iphlpsvc) 服务正在运行。如果该服务被禁用，即使注册表中存在配置，端口转发也不会生效。
- 即时生效机制: 使用 netsh 命令修改时，系统会通过内部 API 通知 IP Helper 服务重新加载配置，因此通常无需重启。但如果手动编辑注册表，则可能需要重启 iphlpsvc 服务或重启计算机才能生效。
- 权限要求: 由于涉及 HKEY_LOCAL_MACHINE 下的系统服务参数，执行该命令或修改对应注册表项均需要管理员权限。
- 验证方法: 你可以通过以下命令查看当前已配置的转发规则，其输出内容直接对应上述注册表键值：

```cmd
netsh interface portproxy show all
```
