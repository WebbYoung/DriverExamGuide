# DriverExamGuide（已归档）

> 这个仓库是 **DriverExam 的第一代桌面实现**，代码已并入 [WebbYoung/DriverExam](https://github.com/WebbYoung/DriverExam) 的 [`desktop/`](https://github.com/WebbYoung/DriverExam/tree/master/desktop) 目录，后续开发在那里进行。
>
> 本仓库保留原样作为 2025-09 的留档快照，不再更新（唯一例外是清除源码里误提交的数据库凭据）。
>
> 📦 **本仓库已在 GitHub 上设为 archived（只读）**：不能提交、开 issue、开 PR。需要改动请去 [DriverExam](https://github.com/WebbYoung/DriverExam)。

## 这里是什么

驾考科目一 / 科目四的 Windows 桌面刷题程序：

- **WinForms / .NET 9**（`net9.0-windows`），单项目 `DriverExamMax`
- 题库以文件形式内置在 `DriverExamMax/Resources/`：科目一 505 题、科目四 40 题，每题一个 `Question.txt` 加可选配图
- 账号与答题进度存 MySQL（`UserInfo` 表：`UserName` / `UserPwd` / `CorrectNum` / `CurrentNum`）
- 数据访问直接走 MySqlConnector，**全部代码里没有任何 HTTP 调用** —— 这也是后来另起 Web API 重做的原因

## 为什么被取代

第一代把「界面 + 业务 + 数据访问 + 凭据」全塞在一个 WinForms 项目里，题目靠绝对路径读取，只能在作者本机跑。第二代（[DriverExam](https://github.com/WebbYoung/DriverExam)）拆成 `Driver.API` / `Driver.Service` / `Driver.DTO` / `Driver.IService` 分层结构，EF Core + SQLite，JWT 鉴权、图形验证码、题目导航与判卷都搬到了服务端。

两代共用同一套题库格式与实体语义，所以合并进了同一个仓库：

```
[题干]
[A、违章行为]
[F-B、违法行为]      ← F- 前缀标出正确项
[C、过失行为]
[D、违规行为]
[解析文字]
```

## 与合并版的差异

| | 本仓库（留档） | DriverExam 的 desktop/ |
| --- | --- | --- |
| 数据库连接串 | 已改为环境变量 / `appsettings.json` 读取 | 同 |
| 题库路径 | 硬编码 `E:\VSprogram\...` | 改为 `AppContext.BaseDirectory/Resources` 并随构建复制 |
| 安全修复 | 仅清凭据 | 清凭据 + 路径可移植 |

想看能直接跑起来的版本、以及后续的 API 端，请到 [WebbYoung/DriverExam](https://github.com/WebbYoung/DriverExam)。

## 安全提醒

`DriverExamMax/SqlManager.cs` 早期版本硬编码过本机 MySQL 的 root 密码，已从当前代码中移除，但**仍留在 Git 历史里**（公开仓库等于已泄露），请更换该数据库密码。现在的连接串从环境变量 `DRIVEREXAM_DB` 或程序目录下的 `appsettings.json` 读取，示例见 [`DriverExamMax/appsettings.Example.json`](DriverExamMax/appsettings.Example.json)。
