# 个人 GitHub 股票日报配置

本 Fork 已预设：模型 `gpt-6.1-sol`、OpenAI 官方接口、Tavily 搜索、A 股大盘复盘、北京时间工作日 18:17、完整文本/HTML 邮件、同一邮箱收发。未指定自选股时不会分析上游示例股票。

## 你只需填写的内容

打开 [Actions Secrets](https://github.com/jh8znwjjc9-droid/daily_stock_analysis/settings/secrets/actions)，点击 **New repository secret**，逐项创建：

| Name（原样复制） | Secret（由你自己填写） |
| --- | --- |
| `OPENAI_API_KEY` | 现有 OpenAI API Key，需有 gpt-6.1-sol 的调用权限 |
| `TAVILY_API_KEYS` | Tavily 免费账户的一个 API Key |
| `EMAIL_SENDER` | 用来收发日报的完整邮箱地址 |
| `EMAIL_PASSWORD` | 该邮箱的 SMTP 授权码/应用专用密码 |

邮箱地址本身不能授权程序发信，因此第四项无法省略。不要填写网页登录密码，也不要把凭据写入仓库文件或聊天。无需另外配置 Variables、模型名或收件人。GitHub 不允许保存空 Secret，因此这些项目保持未创建，填入真实值后保存即可。

推荐使用已有的 QQ/163 邮箱：在邮箱网页设置中开启 SMTP，按邮箱提示取得授权码；若使用 Gmail，需要账户允许应用专用密码。Outlook/Microsoft 365 的现代认证限制可能使本项目的用户名/密码 SMTP 登录不可用。当前设置会由该邮箱给自己发送日报；Dot 需已连接并能读取这个邮箱。

## Tavily 免费 API 申请流程

1. 打开 [Tavily 控制台](https://app.tavily.com/)，注册或登录，完成邮箱验证。
2. 保持 **Researcher / Free** 免费方案。**不要升级付费套餐，不开启 Pay-as-you-go，不绑定付款卡。**
3. 在控制台找到 **API Keys**，复制已有 Key，或创建一个命名为 `daily-stock-analysis` 的 Key。Key 通常以 `tvly-` 开头。
4. 回到上面的 GitHub Secrets 页面，新建 `TAVILY_API_KEYS`，粘贴 Key，保存。
5. 在 Tavily 的 Usage/Billing 页面核对当前方案为免费，Pay-as-you-go 关闭；后续可在这里查看额度。

截至 2026-11-12，官方免费方案每月 1,000 credits，无需信用卡；未开启按量计费时，免费额度耗尽会停止服务而不会自动收费。项目的 Tavily 搜索使用 basic，一次通常消耗 1 credit；一次日报包含多次搜索，不等于只消耗 1 credit。额度耗尽时新闻搜索可能降级，不能保证每天都有完整新闻。

依据：[Tavily 官方计费说明](https://help.tavily.com/articles/8298987937-billing-credits-usage-limits)、[官方 API 入门](https://docs.tavily.com/)。

## 运行和验证

打开 [每日股票分析](https://github.com/jh8znwjjc9-droid/daily_stock_analysis/actions/workflows/00-daily-analysis.yml)，选择 **Run workflow**：

- 勾选 `check_only`：只检查四项是否已填写，不验证 Key 是否有效，不调用 API、不发邮件。
- 不勾选：按交易日规则实际分析并发送邮件，会消耗现有 OpenAI API 余额/额度。
- 缺少任何必填项：安全跳过，Summary 显示缺少的名称，依赖安装和分析均不执行。
- 配置齐全后：到下一个工作日计划时间自动运行。GitHub 定时任务可能延迟；连续 60 天无仓库活动时，公开仓库的定时工作流可能被 GitHub 停用，需要重新启用。

邮件包含完整文本和 HTML，适合 Dot 读取。后续可在 Dot 中要求按邮件标题“股票智能分析报告”整理市场要点、新闻来源与不确定信息；本次未配置或验证 Dot 的邮箱连接与自动整理任务。SMTP 成功仅表示服务器接受邮件，不保证进入收件箱。

## 费用边界

- GitHub：公开仓库，标准 `ubuntu-latest` 运行器；不使用大型付费运行器，不上传 Actions artifacts，也不启用依赖缓存。
- 搜索：只映射 Tavily Key；你必须保持免费方案、按量计费关闭。没有自动购买或续费操作。
- 行情：使用项目无需付费 Key 的默认数据源，不接入付费行情账户。免费源可能限流或失效。
- 邮件：使用现有免费邮箱 SMTP，受邮箱自身额度和政策限制。
- OpenAI：按你的要求使用现有 API 余额/额度；本配置不会使 API 调用免费。
- 其他上游工作流已移至 `.github/workflows-disabled/` 保留，不参与 Actions 触发，避免无关构建、发布、自动审查和 artifact 存储。

官方依据：[GitHub Actions 计费](https://docs.github.com/en/billing/concepts/product-billing/github-actions)。

## 可选：以后添加自选股

另建 Secret `STOCK_LIST`，例如 `600519,000001`（仅格式示例，并非推荐）。设置后会分析这些股票并合并大盘报告；留空时保持仅大盘。增加股票数量会增加 OpenAI 用量和搜索额度消耗。

## 维护与回滚

个人配置只改工作流和文档，未改应用分析逻辑。同步上游时保留本工作流；不要直接恢复全部上游定时任务。可通过 GitHub 提交历史还原本次配置提交，或将所需原工作流从 `.github/workflows-disabled/` 移回。此文档仅面向这个中文个人部署，未新增英文版本。