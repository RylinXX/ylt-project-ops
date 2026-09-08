# 营力特 · 工程运营管理平台
**YLT ProjectOps** · 项目台账、费用凭证、人员档案与企业资料管理。

面向建筑工程公司的日常运营，将项目、费用、合同、人员和企业资质集中管理。它不是设备维修工具，也不同于独立的运输对账系统。

[线上平台](https://pam.etgq.com/) · [部署目录](deploy/)

## 核心业务

| 业务 | 内容 |
| --- | --- |
| 项目与看板 | 项目台账、经营汇总、费用分类与状态查询 |
| 费用凭证 | 凭证录入、附件保存、分类、审核与导出 |
| 人员管理 | 人员档案及相关资料维护 |
| 合同与资质 | 合同、企业证照和有效期相关管理 |
| 文件处理 | Excel 导入导出，可选模型辅助 OCR，人工复核 |

OCR 结果不能替代原始凭证；金额、人员和合同信息应经过业务人员确认。

## 技术与目录

Python 3.11+、Flask、SQLite、Jinja2、openpyxl，支持 Docker Compose 部署。

```text
construction_maintenance/  应用、页面、静态资源与业务逻辑
contract_templates/       合同模板
tests/                    自动化测试
deploy/                   反向代理等配置
scripts/                  导入和辅助脚本
pyproject.toml            依赖及测试配置
```

## 本地开发

在隔离环境中运行，不要使用线上数据库作为测试数据。

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install -e '.[dev]'
```

启动前配置 `CAM_SECRET_KEY`、`CAM_ADMIN_USERNAME` 和 `CAM_ADMIN_PASSWORD_HASH`。管理员密码使用 Werkzeug 哈希，不是明文。仅本地 HTTP 调试可将 `CAM_SESSION_COOKIE_SECURE` 设为 `0`。

```sh
flask --app construction_maintenance run --host 127.0.0.1 --port 5000
pytest
```

认证和 CSRF 防护在生产环境应保持开启。不要在生产环境启用 `CAM_SEED_DEMO_DATA` 或执行演示数据脚本。

## 生产部署

域名 `pam.etgq.com` 经 Nginx 转发到容器服务。现有 Compose 使用 `ylt_pam_data` 保存数据库、上传与导出文件。不要删除这个数据卷，也不要因仓库改名更改卷名、容器名或数据库路径。

生产密钥通过部署环境注入；OCR 的 `ARK_API_KEY` 等可选配置不能进入 Git。发布前备份数据卷、配置和当前镜像，检查健康状态、登录、凭证查询和导出流程。

仓库是代码版本，不包含最新业务数据。根目录历史 `Carsystem` 引用不属于本平台的运行依赖，运输运营平台单独维护。
