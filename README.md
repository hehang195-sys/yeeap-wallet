# yeeap-wallet

![Version](https://img.shields.io/badge/version-0.3.8-blue.svg)
![NPM](https://img.shields.io/badge/npm-yeeap--cli%400.3.8-cb3837.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

**Project URL:** [https://github.com/hehang195-sys/yeeap-wallet](https://github.com/hehang195-sys/yeeap-wallet)

**payment-infra-rd** 类技能：为第三方业务技能执行 yeeap 钱包支付交易。业务 Skill Phase 2 传入 `order_no` + `app_id`，本技能通过 **yeeap-cli** 读取本地订单文件并完成授权与支付。

---

## 安装

```bash
npx -y skills add "hehang195-sys/yeeap-wallet" --agent '*' -g -y
```

安装后**完全退出并重启 Agent 客户端一次**，然后在对话中声明：**使用技能 `yeeap-wallet`**。

---

## 调用协议

业务技能 Phase 1 已将订单写入 `~/.yeeap/orders/<app_id>/<order_no>.json` 后，Phase 2 只向 `yeeap-wallet` 传入 `order_no` 与 `app_id`。

具体命令模板、npm 环境锁定、授权查询、支付结果分流与禁止项均以 [SKILL.md](./SKILL.md) 为准。不要从 README 复制裸 `npx` 命令执行支付、授权或查询。

---

## 核心文件

| 文件 | 说明 |
|------|------|
| [SKILL.md](./SKILL.md) | Agent 调用协议（pay-context / auth-init-context / check-auth-context 与 stdout 分流） |
| [IMPORTANT_STATEMENTS.md](./IMPORTANT_STATEMENTS.md) | 技能来源追溯、CLI 鉴权原理、出站白名单、触发边界 |

---

## 权限与出站

- **CLI 安装声明**：`npm:yeeap-cli@0.3.8`（锁定版本，不使用 `@latest`）
- **出站白名单**：
  - `registry.npmjs.org`（Preflight 与 CLI 安装/执行）
  - `qaap.yeepay.com/yeeap`（Open API：下单、授权、查询）

---

## 安全模型

- 全程**不读取** `.env`、配置文件或环境变量中的私钥
- 全程**不要求**用户提供支付密码
- 授权采用**一次性短效令牌**，`pay-context` 提交支付时必须携带本地 token，需用户人类确认后方可完成支付
- `pay-query` 仅返回订单状态，不返回也不写入 `payCredential`；只有 `pay-context` 输出 `已获取到支付凭证` 后，业务 Skill 才能进入下一阶段
- 严格支付/授权命令必须使用 CLI 支付上下文模式，避免同机多 Agent 窗口串用 agentId
- Agent **禁止自动轮询**授权状态

详见 [IMPORTANT_STATEMENTS.md](./IMPORTANT_STATEMENTS.md)。

---

## License

MIT
