# 项目设置

## 1. dbt_project.yml

每个 DBT 项目都必需一个 dbt_project.yml 文件，这是让 DBT 知道此目录是 DBT 项目，同时它还包含告诉 DBT 如何操作项目的重要信息。

- DBT 配置文件使用 YAML 格式书写内容。
- 默认情况下，DBT 在当前工作目录及其父目录中查找 dbt_project.yml，但我们可以使用 `--project-dir` 标志或 `DBT_PROJECT_DIR` 环境变量设置不同的目录。
- 使用 DBT Cloud 配置下的 `project-id` 在 dbt_project.yml 文件中指定 DBT Cloud 项目 ID。在 DBT Cloud 项目 URL 中查找项目 ID，例如在 https://YOUR_ACCESS_URL/11/projects/123456 中，项目 ID 为 123456。
- 请注意，如果不是配置（例如宏），则无法在 dbt_project.yml 文件中设置“**属性**”，这适用于所有类型的资源。

### 1.1 dbt_project.yml 示例


```yaml linenums="1"
name: string
config-version: 2
version: version
profile: profilename
model-paths: [directorypath]
seed-paths: [directorypath]
test-paths: [directorypath]
analysis-paths: [directorypath]
macro-paths: [directorypath]
 snapshot-paths: [directorypath]
docs-paths: [directorypath]
asset-paths: [directorypath]
packages-install-path: directorypath
clean-targets: [directorypath]
query-comment: string
require-dbt-version: version-range | [version-range]

flags:
  <global-configs>

dbt-cloud:
  project-id: project_id # Required
  defer-env-id: environment_id # Optional

quoting:
  database: true| false
  schema: true | false
  identifier: true | false

metrics:
  <metric-configs>

models:
  <model-configs>

seeds:
  <seed-configs>

semantic-models:
  <semantic-model-configs>

saved-queries:
  <saved-queries-configs>

snapshots:
  <snapshot-configs>

sources:
  <source-configs>

tests:
  <test-configs>

vars:
  <variables>

on-run-start: sql-statement |[sql-statement]
on-run-end: sql-statement |[sql-statement]

dispatch:
  - macro_namespace: packagename
    search_order: [packagename]

restrict-access: true | false
```

### 1.2 命名约定

对于 dbt_project.yml 文件中的配置，遵循正确的 YAML 命名约定非常重要，以确保 DBT 可以正确处理它们。

- 在 dbt_project.yml 文件中配置具有多个单词的资源类型时，请使用破折号 (`-`)。以下是已保存查询的示例：

    ```yaml linenums="1" title="dbt_project.yml" hl-lines="1-2"
    saved-queries:  # Use dashes for resource types in the dbt_project.yml file.
      my_saved_query:
        +cache:
          enabled: true
    ```

- 为 dbt_project.yml 文件以外的 YAML 文件配置多个单词的资源类型时，请使用下划线（`_`）。例如，以下是在 semantic_models.yml 文件中保存的相同查询资源：

    ```yaml linenums="1" title="dbt_project.yml" hl-lines="1-2"
    saved-queries:  # Use dashes for resource types in the dbt_project.yml file.
      - name: saved_query_name
        ...
        config:
          +cache:
            enabled: true
    ```

## 2. .dbtignore

