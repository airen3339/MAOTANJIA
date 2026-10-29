# 贸探家 MAOTANJIA

> 一套面向中国外贸企业的海外买家线索搜索引擎，以及面向海外采购商的中国工厂检索门户。
> Global trade scout: find overseas buyers by city and industry, or help global buyers find Chinese factories.

![brand](https://img.shields.io/badge/brand-MAOTANJIA-0B2A6B) ![backend](https://img.shields.io/badge/backend-Spring%20Boot%203.2-6DB33F) ![frontend](https://img.shields.io/badge/frontend-Vue%203%20%2B%20Vite-42B883) ![db](https://img.shields.io/badge/database-MySQL%208-4479A1)

---

## 项目简介

贸探家把「选市场 + 选行业 + 批量找店铺 + 拿到联系方式 + 留档跟进」变成一个可重复的在线流程。

用户按 **国家 → 省/州 → 地区 → 城市 → 行业/产品** 选择目标市场，系统通过大模型与真实网络数据源，检索当地销售、批发、进口、代理同类产品的店铺与企业，输出名称、地址、电话、邮箱、WhatsApp、网站等结构化线索，并落库为历史记录与客户库。

### 双门户

| 门户 | 面向 | 默认语言 / 币种 | 说明 |
|---|---|---|---|
| 贸探家（买家门户） | 中国外贸企业 | 中文 / CNY | 搜海外店铺、批发商、进口商 |
| FindChinaFactory（厂家门户） | 海外采购商 | English / USD | 搜中国工厂与供应商 |
| 总管理后台 | 平台运营 | 中文 | 模型、字典、用户、额度、商家、代理、审计 |
| 代理后台 | 城市/区域/金牌代理 | 中文 | 商家录入、推广、佣金、提现 |

四个前端共用一套后端与数据库，生产环境由 Nginx 按域名分发。

---

## 功能特性

- **多维检索**：国家/地区/城市级联 + 行业类目 + 关键词 + 企业类型 + 目标条数。
- **结构化线索**：名称（原文/译文）、地址、电话、WhatsApp、邮箱、网站、社媒、主营产品、置信度、来源。
- **异步任务**：排队/执行/完成/失败/取消状态机，进度轮询，失败重跑。
- **结果落库**：任务条件快照 + 线索 + 统计全部入库，服务端分页、筛选、排序。
- **历史与客户库**：重跑、复制条件、增量对比；收藏、加入客户库、阶段管理、纠错。
- **导出中心**：XLSX / CSV（UTF-8 BOM）/ vCard，买家中文表头，厂家英文表头。
- **订阅与额度**：注册自动开通免费版，赠 3 次搜索；预扣、失败返还、缓存命中不扣次。
- **会员权益与升级**：席位、每日导出、联系方式验证、开放 API、模型档位与历史保留按套餐生效；同档续费顺延叠加、升档按剩余天数折抵、降档到期后切换。
- **自助支付**：后台开关切换 Stripe 在线支付或微信/支付宝扫码收款；扫码由后台上传收款码、买家提交凭证、运营确认到账后自动发放套餐。
- **可配置数据源**：Serper / Google Places Key 可在后台填写并测试连通性；未配置任何真实 Key 时拒绝创建任务，系统不提供演示数据。
- **大模型可切换**：后台维护 OpenAI 兼容 Provider，内置 DeepSeek 预设（flash / v4-pro），API Key 加密存储，支持连通性测试。
- **成本护栏**：`llm_usage` 记录 token 与估算成本，后台看板按今日/本月/任务/模型汇总，单任务超 ¥0.8 标红；支持「仅空闲时段执行」排队。
- **证据与验证标注**：线索标注进口证据等级（未接海关数据统一「未核验」）、渠道角色、联系方式验证状态与时间；结果可筛选，导出同步标注。
- **行业模板化**：行业词库、HS 编码、渠道角色、属性槽位与数据覆盖等级后台可配；Places 默认只做地址核验。
- **合规触达**：客户库含营销状态与下次跟进；一对一邮件模板预览，不提供一键群发；退订/删除/更正在线提交并由后台处理。
- **APP 下载页**：买家与厂家门户提供独立下载页，二维码本地生成；Android APK / App Store 地址后台可配，未配置时引导安装 PWA。
- **RFQ 询盘闭环**：买家在商家页发起询盘（产品/数量/目标价/目的国），买家、平台运营、所属代理商三端消息线程，支持回复、关闭与状态流转。
- **响应统计与信任徽章**：商家页展示响应率、平均响应时长与累计询盘数，作为买家决策依据。
- **入驻商家**：审核、会员级别、保证金、商家主页、搜索固定优先、每页 3 个入驻位。
- **代理商体系**：商家归属、推广链接、佣金规则快照、结算与提现、90 天保护期。
- **开放 API**：API Key、吊销/轮换、可选 HMAC 签名、限流、Swagger 文档。
- **PWA/H5**：移动端布局、拨号、WhatsApp 直达、地图导航、分享。
- **安全**：JWT + RBAC、组织隔离、BCrypt、AES-GCM、SSRF 防护、限流、登录锁定、审计日志。

---

## 技术栈

| 层 | 技术 |
|---|---|
| 后端 | Java 17、Spring Boot 3.2.5、Spring Security、Spring JDBC (JdbcTemplate)、Flyway、MySQL 8、JJWT、Springdoc OpenAPI |
| 检索 | Serper、Google Places、Jsoup 页面抓取、LLM 结构化抽取（仅真实数据，无演示模式） |
| 导出 | Apache POI（XLSX）、CSV、vCard |
| 前端 | Vue 3.4、TypeScript、Vite 5、vue-router（hash）、自建设计系统（无 UI 库） |
| 部署 | Docker / Docker Compose、Nginx、MySQL 8 |

> 未引入 Redis / MinIO / JPA：额度使用 MySQL 事务与行锁，上传使用本地磁盘，便于单机部署与小团队运维。

---

## 快速开始

### 1. 准备

- JDK 17、Maven 3.9+
- Node.js 18+、npm 9+
- MySQL 8.0+

```sql
CREATE DATABASE maotanjia DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 2. 启动后端

```bash
cd backend
export DB_URL='jdbc:mysql://127.0.0.1:3306/maotanjia?useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true&useSSL=false'
export DB_USER=root
export DB_PASSWORD=root
mvn spring-boot:run
```

健康检查：`http://localhost:8080/actuator/health` → `{"status":"UP"}`

首次启动自动执行 Flyway 迁移（V1–V9）并创建管理员：

- `admin@maotanjia.com` / `Admin@123456`（生产必须通过环境变量覆盖并改密）

### 3. 启动前端

```bash
cd frontend
npm install
npm run dev
```

| 入口 | 开发地址 |
|---|---|
| 买家门户 | `http://localhost:5173/buyer.html` |
| 厂家门户 | `http://localhost:5173/supplier.html` |
| 总后台 | `http://localhost:5173/admin.html` |
| 代理后台 | `http://localhost:5173/agent.html` |

> **默认管理账号**：`admin@maotanjia.com` / `Admin@123456`（首次启动自动创建，生产必须用 `ADMIN_EMAIL` / `ADMIN_PASSWORD` / `ADMIN_NAME` 覆盖并改密）。
> 买家、厂家门户为自助注册（注册赠 3 次搜索）；代理后台无预置账号，需在总后台「代理」菜单中创建并填写登录邮箱与初始密码。
> 四端入口、端口与账号细节见 [功能说明 1.3](./功能说明.md#13-系统入口端口与默认账号)。

### 4. 真实数据源（必须配置）

系统只保存真实检索数据，不再提供演示模式。本地启动后需在后台「设置 → 搜索引擎数据源」填写 `SERPER_API_KEY` / `GOOGLE_PLACES_KEY`，或通过环境变量配置；后台提供连通性测试。未配置任何 Key 时创建任务会被拒绝且不扣额度。

---

## Docker 部署

```bash
cp deploy/.env.example .env
# 修改数据库密码、JWT_SECRET、APP_CRYPTO_SECRET、管理员账号与检索 Key
docker compose up -d --build
docker compose logs -f api
```

- 前端容器内置 Nginx，按 `deploy/nginx.conf` 分发四个域名。
- 数据卷：`mysql-data`、`uploads`。
- 备份/恢复：`bash deploy/backup.sh`、`bash deploy/restore.sh <file.sql.gz>`。

完整步骤（含 HTTPS、备份、升级、排错）见 [环境搭建](./环境搭建.md)。

---

## 目录结构

```
.
├─ backend/                 Spring Boot API
│  ├─ src/main/java/com/maotanjia/
│  │  ├─ controller/        认证、搜索、线索、导出、公共、后台、代理、开放 API
│  │  ├─ service/           搜索、额度、商家、代理、导出、大模型
│  │  ├─ service/search/    数据源、抓取、抽取、入驻合并
│  │  ├─ config/            安全、JWT、限流、异步、上传、加密
│  │  └─ common/            响应、异常、分页、SSRF、JSON
│  └─ src/main/resources/db/migration/  Flyway V1–V9
├─ frontend/                Vue 3 四端
│  ├─ src/buyer/            买家门户
│  ├─ src/supplier/         厂家门户（英文）
│  ├─ src/admin/            总管理后台
│  ├─ src/agent/            代理后台
│  └─ src/shared/           设计系统、请求、会话、组件
├─ deploy/                  Nginx、备份/恢复、.env.example
├─ design/                  LOGO、APP 图标、品牌预览
├─ docs/                    计划、功能、环境、宣传、README
├─ docker-compose.yml
└─ .gitignore
```

---

## 开放 API 示例

```bash
curl -X POST https://www.maotanjia.com/api/v1/open/v1/search \
  -H "Content-Type: application/json" \
  -H "X-API-Key: mtj_xxxxxxxx" \
  -d '{"countryCode":"RU","cityCode":101,"industryId":1,"keyword":"hardware","targetCount":50}'
```

```json
{
  "code": 0,
  "message": "ok",
  "data": { "id": 123, "status": "QUEUED" },
  "traceId": "b9b843bb6187"
}
```

| 方法 | 路径 | 说明 |
|---|---|---|
| POST | `/api/v1/open/v1/search` | 创建任务 |
| GET | `/api/v1/open/v1/search/{id}` | 任务状态 |
| GET | `/api/v1/open/v1/search/{id}/leads` | 分页线索 |
| GET | `/api/v1/open/v1/leads?taskId=` | 按任务获取线索 |
| GET | `/api/v1/open/v1/me` | Key 归属信息 |

签名（可选）：`X-Timestamp` + `X-Signature = HMAC-SHA256(secret, method + path + timestamp + body)`。
在线文档：启动后访问 `/swagger-ui.html`。

---

## 多语言

- 买家门户与运营/代理后台为中文；厂家门户默认英文，支持 **English / Русский / Español / Português / Français / Deutsch / العربية** 七种界面语言。
- 语言入口在导航栏、登录注册页与登录后顶栏，切换即时生效；选择保存在本地，刷新后保持。
- 语言优先级：用户手动选择 > 账号语言 > 注册国家语言 > 浏览器语言 > 英语。
- 在注册或账户中切换国家时自动切换对应语言（美国→英语、俄罗斯→俄语、拉美/西班牙→西班牙语、巴西/葡萄牙→葡萄牙语、法国→法语、德国→德语、中东→阿拉伯语），未覆盖国家回退英语。
- 字典保存中/英/本地名称；线索保留原文与译文。
- 阿拉伯语自动启用 `dir="rtl"`，已完成基础镜像布局适配。

---

## 安全说明

- 生产必须替换 `JWT_SECRET`、`APP_CRYPTO_SECRET`、数据库密码与管理员密码。
- 生产必须启用 HTTPS，且不要对公网暴露 3306/8080。
- Swagger 建议仅内网开放或关闭。
- 仅采集企业公开商业信息，保留来源，提供纠错与删除通道。

---

## 文档

- [功能说明](./功能说明.md)
- [环境搭建与部署](./环境搭建.md)
- [项目计划与进度](./01-项目计划与进度.md)
- [厂家搜索项目计划](./02-厂家搜索项目计划.md)
- [功能测试报告（2026-10-26）](./06-测试报告-20261026.md)
- [竞品调研与优化建议](./07-竞品调研与优化建议.md)
- [真实数据保障与数据新鲜度改造计划](./08-真实数据保障与数据新鲜度改造计划.md)
- [公众号宣传稿](./公众号宣传.md)
- 品牌预览：[design/preview.html](../design/preview.html)

---

## 当前状态与路线图

已完成 M0–M20：基础设施、字典、搜索链路、买家 Web、PWA/H5、管理后台、开放 API、安全交付、入驻商家与代理商体系、支付与数据源配置、RFQ 询盘闭环、厂家门户多语言、APP 下载入口、会员权益与套餐升级、线索证据与可触达性、行业模板化、实体归并与验证、合规触达、DeepSeek 成本护栏、真实数据保障与数据新鲜度。

M20 已完成：移除全部演示数据与 Mock 回退、真实数据源硬校验（无 Key 拒建任务、空结果失败并退款）、来源逐条归因与模型版本追溯、`lead_task` 多任务关联、超过 30 天线索在下次搜索时刷新、新鲜度体系与历史保留治理，详见 [改造计划](./08-真实数据保障与数据新鲜度改造计划.md)。

升级注意：先执行 Flyway `V10__data_integrity.sql`，再执行 `deploy/ops/cleanup-mock.sql` 备份并清理存量演示数据。

已知简化（详见 [计划文档 9.3.1](./01-项目计划与进度.md#931-已知-todo按优先级)）：

- 在线支付需商户自行申请 Stripe 账号并填写密钥；扫码支付依赖后台上传收款码与运营确认。
- 地区字典已补全 17 国省/州与主要城市（223 个省级、555 个城市节点），仍可在后台增补。
- 移动端为 PWA/H5，未打包 Android/iOS 安装包；「下载 APP」页的安装包/商店地址可在后台「设置」填写，未填写时引导安装 PWA。
- 真实检索依赖 Serper / Google Places Key；未配置时拒绝创建任务，检索失败或空结果置 `FAILED` 并返还额度，不做演示回退。
- 大模型仅支持 OpenAI 兼容协议；DeepSeek 预设需填写 Key、测试连通性后手动启用，未配置前使用规则引擎/通用 Provider。
- 进口证据：未接入海关数据源，所有线索 `evidence_level` 统一为「未核验」，覆盖等级默认推定级，不伪造提单级。
- 联系方式验证：仅做格式、域名存活、MX/A 记录与电话区号位数校验；未做抽样发信（无内置 SMTP），每任务最多验证 12 条。
- 合规触达：一对一模板只生成预览与 `mailto`，平台不提供群发；退订/删除/更正由后台人工处理。
- 商家询盘已支持（买家发起、运营/代理代答、响应统计）；商家仍无自助登录，认领（Claim）待做。
- 会员权益中的席位上限、验证额度、模型档位与历史保留已落库并在账单展示；成员邀请、模型路由与历史清理尚未上线。
- 保证金退还/扣罚、推广二维码、对象存储与水印待实现。

后续优先级：真实数据硬约束与数据新鲜度（M20）→ 真实数据源与 DeepSeek Key 校准回归 → 短名单与供应商对比 → 保存搜索与提醒 → 商家认领 → 行业数据包/API 授权 → 交易担保 → 移动端打包与对象存储。

---

## 贡献

1. Fork 并创建功能分支：`feat/your-feature`
2. 提交前确保 `mvn -q -DskipTests package` 与 `npm run build` 通过
3. 提交 Pull Request，说明背景、改动与验证方式

数据库迁移只允许新增 `V10__xxx.sql` 及以后版本，不要修改已执行的迁移文件。

---

## 联系

- 品牌：贸探家 MAOTANJIA
- 买家门户：`www.maotanjia.com`
- 厂家门户：`www.findchinafactory.com`
- 商务与合作：通过公众号「贸探家」留言

---

## License

当前仓库未附开源许可证。如需开源，请补充 `LICENSE` 并明确授权范围；在补充前默认保留所有权利。

---

## 🏢 关于我们

<p align="center">
  <a href="http://www.net188.net">
    <img src="http://www.net188.net/images/logo1.png" alt="Net188 Logo" width="200" />
  </a>
</p>

<p align="center">
  <strong>Net188 · 互联网技术服务</strong>
</p>

<p align="center">
  专注于跨平台应用开发、AI Agent 集成与大模型应用落地。<br/>
  提供从产品设计、开发实施到部署运维的全栈技术解决方案。
</p>

<p align="center">
  🌐 <a href="http://www.net188.net"><strong>www.net188.net</strong></a>
</p>

---
