# 通用属性

## 1. columns

`columns` 可以应用于 `models`、`sources`、`seeds`、`snapshots`、`analyses`。

=== "Models"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    models:
      - name: <model_name>
        columns:
          - name: <column_name>
            data_type: <string>
            description: <markdown_string>
            quote: true | false
            tests: ...
            tags: ...
            meta: ...
          - name: <another_column>
            ...
    ```

=== "Sources"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    sources:
      - name: <source_name>
        tables:
        - name: <table_name>
          columns:
            - name: <column_name>
              data_type: <string>
              description: <markdown_string>
              quote: true | false
              tests: ...
              tags: ...
              meta: ...
            - name: <another_column>
              ...
    ```

=== "Seeds"

    ```yaml title="seeds/`<filename>`.yml" linenums="1"
    version: 2

    seeds:
      - name: <seed_name>
        columns:
          - name: <column_name>
            data_type: <string>
            description: <markdown_string>
            quote: true | false
            tests: ...
            tags: ...
            meta: ...
          
          - name: <another_column>
            ...
    ```

=== "Snapshots"

    ```yaml title="snapshots/`<filename>`.yml" linenums="1"
    version: 2

    snapshots:
      - name: <snapshot_name>
        columns:
          - name: <column_name>
            data_type: <string>
            description: <markdown_string>
            quote: true | false
            tests: ...
            tags: ...
            meta: ...
          
          - name: <another_column>
            ...
    ```

=== "Analyses"

    ```yaml title="analyses/`<filename>`.yml" linenums="1"
    version: 2

    analyses:
      - name: <analysis_name>
        columns:
          - name: <column_name>
            data_type: <string>
            description: <markdown_string>
          
          - name: <another_column>
            ...
    ```

列本身并不是资源。相反，它们是另一种资源类型的子属性，它们可以定义与资源级别定义的属性类似的子属性：`tags`、`meta`、`tests`、`description`。

因为列不是资源，所以它们的 `tags` 和 `meta` 属性不是真正的配置。它们不会继承其父资源的 `tags` 或 `meta` 值。但是，我们可以选择在列上定义的通用测试，使用应用于其列或顶级资源的标签；

列可以选择定义 `data_type`，这是必要的：

- 执行模型规范
- 在其他包或插件中使用，例如 `sources` 和 `dbt-external-tables` 的 `external` 属性

## 2. config

`config` 属性允许我们在 YAML 文件中定义属性的同时配置资源，可以应用于 `models`、`sources`、`seeds`、`snapshots`、`tests`、`metrics`、`exposures`、`semantic models`、`saved queries`。

=== "Models"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    models:
      - name: <model_name>
        config:
          <model_config>: <config_value>
          ...
    ```

=== "Seeds"

    ```yaml title="seeds/`<filename>`.yml" linenums="1"
    version: 2

    seeds:
      - name: <seed_name>
        config:
          <seed_config>: <config_value>
          ...
    ```

=== "Snapshots"

    ```yaml title="snapshots/`<filename>`.yml" linenums="1"
    version: 2

    seeds:
      - name: <snapshot_name>
        config:
          <snapshot_config>: <config_value>
          ...
    ```

=== "Tests"

    ```yaml title="tests/`<filename>`.yml" linenums="1"
    version: 2

    <resource_type>:
      - name: <resource_name>
        tests:
          - <test_name>:
              <argument_name>: <argument_value>
              config:
                <test_config>: <config_value>
                ...
        
        columns:
          - name: <column_name>
            tests:
              - <test_name>
              - <test_name>:
                  <argument_name>: <argument_value>
                  config:
                    <test_config>: <config_value>
                    ...
    ```

=== "Sources"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    sources:
      - name: <source_name>
        config:
          <source_config>: <config_value>
        
        tables:
          - name: <table_name>
            config:
              <table_config>: <config_value>
    ```

=== "Metrics"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    metrics:
      - name: <metric_name>
        config:
          enabled: true | false
          group: <string>
          meta: {dictionary}
    ```

=== "Exposures"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    exposures:
      - name: <exposure_name>
        config:
          enabled: true | false
          meta: {dictionary}
    ```

=== "Semantic models"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    semantic_models:
      - name: <exposure_name>
        config:
          enabled: true | false
          group: <string>
          meta: {dictionary}
    ```

=== "Saved queries"

    ```yaml title="models/`<filename>`.yml" linenums="1"
    version: 2

    saved_queries:
      - name: <saved-query-name>
        config:
          cache:
            enabled: true | false
          enabled: true | false
          export_as: view | table
          group: <string>
          meta: {dictionary}
    ```

## 3. contraints

约束是许多数据平台的一个功能。指定后平台将在数据填充到新表中或插入到预先存在的表中时，会先对数据执行额外的验证。如果验证失败，则表创建或更新失败，操作将回滚，并且您将看到清晰的错误消息。

强制执行时，约束可保证我们永远不会在模型具体化的表中看到无效数据，不同数据平台的执行情况差异很大。

约束条件需要声明和执行模型规范。约束永远不会应用于短暂的模型或具体化为视图的模型，只有表模型和增量模型支持应用和强制约束。

### 3.1 定义约束

可以为单个列定义约束，也可以在模型级别为一个或多列定义约束。作为一般规则，我们建议直接在这些列上定义单列约束。

如果为单个模型定义多个 `primary_key` 约束，则必须在模型级别定义这些约束。不支持在列级别定义多个 `primary_key` 约束。

### 3.2 特殊平台支持

### 3.3 自定义约束






