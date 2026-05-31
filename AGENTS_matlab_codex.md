# 本机 Codex 与 MATLAB 连接说明

## 目的

这份文件记录本机 Codex 与 MATLAB R2017a 的连接配置、已验证结果和后续使用规则。后续在这台电脑上处理 MATLAB 自动化任务时，应优先参考本文件。

## 本机环境

- 操作系统环境：Windows / Windows PowerShell。
- MATLAB 主程序路径：`D:\matlab\bin\matlab.exe`。
- MATLAB 版本：R2017a。
- `version; ver;` 已验证输出：`MATLAB 9.2.0.538062 (R2017a)`，`Simulink 8.9 (R2017a)`。
- 用户习惯：通常会先手动打开 MATLAB 桌面界面，再让 Codex 执行 MATLAB 代码。

## Codex MCP 当前状态

- Codex 全局配置文件：`C:\Users\Sky丶至高天\.codex\config.toml`。
- 当前应保留并优先使用的 MCP server：`matlab_r2017a_com`。
- `matlab_r2017a_com` 使用 Windows COM 自动化连接 MATLAB，适合 R2017a 和“已打开 MATLAB 桌面”的工作方式。
- `matlab_r2017a_com` server 脚本路径：`C:\Users\Sky丶至高天\.matlab\agentic-toolkits\codex\matlab-r2017a-com-server.ps1`。
- 官方 MATLAB MCP server 可执行文件仍可能存在：`C:\Users\Sky丶至高天\.matlab\agentic-toolkits\bin\matlab-mcp-core-server.exe`。
- 但官方 `[mcp_servers.matlab]` 已从 Codex 配置中禁用/删除，因为它在本机 R2017a 环境下容易超时，且官方支持范围比 R2017a 新。

## 重要结论

- 以后用户可以直接说“运行 `version; ver;`”或“用 MATLAB 跑这段代码”，不需要每次显式说 `matlab-r2017a-automation`。
- Codex 应默认使用 R2017a COM 路径，而不是官方 `matlab` MCP。
- 如果上下文里出现官方 `mcp__matlab__` 工具，要谨慎使用；它曾经在本机调用 `detect_matlab_toolboxes` 时等待 600 秒后超时。
- 对 R2017a 不要使用 `matlab -batch`，该选项属于较新的 MATLAB 版本。
- 较复杂的 MATLAB 任务，优先写成 `.m` 文件再运行，便于用户检查和复用。
- 代码应尽量保持 R2017a 兼容，避免使用 R2017a 之后才引入的 MATLAB 语法/API。
- 不要自动关闭或 `quit` MATLAB，除非用户明确要求。

## 已安装/创建的 Skill 与工具

- 自定义 Codex skill：`C:\Users\Sky丶至高天\.codex\skills\matlab-r2017a-automation`。
- 该 skill 用于提醒 Codex 按 R2017a、COM、先开 MATLAB 桌面的方式执行 MATLAB 自动化。
- MATLAB Agentic Toolkit skills 已链接到 `C:\Users\Sky丶至高天\.codex\skills`。
- 示例链接之一：`matlab-generate-gnss-waveform -> C:\Users\Sky丶至高天\Documents\Codex\matlab-agentic-toolkit\skills-catalog\wireless-communications\matlab-generate-gnss-waveform`。

## 相关工作目录和脚本

- 工作目录：`C:\Users\Sky丶至高天\Documents\Codex\2026-05-31\matlab-matlab-mcp-core-server-https`。
- 安装/配置脚本：`C:\Users\Sky丶至高天\Documents\Codex\2026-05-31\matlab-matlab-mcp-core-server-https\setup-matlab-codex.ps1`。
- 禁用官方 MATLAB MCP 的脚本：`C:\Users\Sky丶至高天\Documents\Codex\2026-05-31\matlab-matlab-mcp-core-server-https\disable-official-matlab-mcp.ps1`。
- R2017a COM MCP server 源文件：`C:\Users\Sky丶至高天\Documents\Codex\2026-05-31\matlab-matlab-mcp-core-server-https\mcp\matlab-r2017a-com-server.ps1`。

## 已验证示例

高斯函数拟合示例已经通过 MATLAB/COM 路径运行成功。

- 示例脚本：`C:\Users\Sky丶至高天\Documents\Codex\2026-05-31\matlab-matlab-mcp-core-server-https\examples\gaussian_fit_r2017a.m`。
- 输出图片：`C:\Users\Sky丶至高天\Documents\Codex\2026-05-31\matlab-matlab-mcp-core-server-https\examples\gaussian_fit_r2017a.png`。
- 拟合结果示例：
  - true amplitude：`3.500000`，fitted amplitude：`3.505310`
  - true mu：`0.800000`，fitted mu：`0.794360`
  - true sigma：`1.200000`，fitted sigma：`1.207397`
  - true offset：`0.400000`，fitted offset：`0.400173`
  - `SSE = 2.119388`
  - `R^2 = 0.987682`

## 故障处理

- 如果 COM 连接 MATLAB 失败，先让用户确认 MATLAB 桌面已经打开。
- 如果仍失败，可以让用户在 PowerShell 中运行一次：

```powershell
"D:\matlab\bin\matlab.exe" -regserver
```

然后重启 MATLAB 和 Codex。

- 如果 Codex 又开始调用官方 `matlab` MCP 并长时间卡住，应检查 `C:\Users\Sky丶至高天\.codex\config.toml`，确认不存在 `[mcp_servers.matlab]` 块，只保留 `[mcp_servers.matlab_r2017a_com]`。

## 本机操作约束

- 禁止批量删除文件或目录。
- 不要使用：`del /s`、`rd /s`、`rmdir /s`、`Remove-Item -Recurse`、`rm -rf`。
- 需要删除文件时，只能一次删除一个明确路径的文件。
- 如果需要批量删除文件，应停止操作，并请求用户手动删除。
