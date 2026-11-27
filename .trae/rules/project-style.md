---
alwaysApply: true
---
# 项目编码与防御性编程规范

## 命名与类型规范
- 变量和函数使用 `snake_case`（如 `get_user_by_id`），类名使用 `PascalCase`（如 `UserService`），常量使用 `UPPER_SNAKE_CASE`（如 `MAX_RETRY_COUNT`）
- 所有函数参数与返回值必须有 Python 3.12 内置类型注解（如 `list[str]`、`str | None`，不使用旧式 `Optional[str]`）
- 所有公共函数和类方法必须包含中文 docstring（功能描述 + 参数说明 + 返回值说明）

## 防御性编程规范
- 所有接受外部输入的函数必须在函数体开头对输入进行有效性校验（API 层统一使用 Pydantic 模型校验）
- 禁止使用裸 `except:` 或宽泛 `except Exception:`，必须捕获具体异常类型并记录日志
- 禁止字符串拼接构造 SQL，必须使用参数化查询或 ORM
- 禁止在代码中硬编码密码、Token、API Key 等敏感信息
- 调用第三方 HTTP 或大模型服务必须设置超时（如 `timeout=10.0`）并做好失败兜底
- 所有文件与数据库连接操作必须使用上下文管理器（`with`）

## API 响应规范
- 创建成功：HTTP 201；查询/更新成功：HTTP 200；删除成功：HTTP 204
- 资源不存在：HTTP 404，返回体格式统一为 `{"detail": "..."}`
- 时间字段统一使用 UTC ISO 格式（`datetime.now(timezone.utc)`）
