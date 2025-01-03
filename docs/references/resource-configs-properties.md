# 资源配置和属性

项目中的资源（例如 `models`、`snapshots`、`seeds` 以及 `tests` 等）有很多声明的属性，此外资源还可以定义配置（一种提供额外功能的特殊属性）。

- 资源的属性在 properties.yml 文件中进行声明，通常嵌套在 `config` 属性下。它们还可以通过 `config()` 宏（就在 .sql 文件中）进行设置，并在 dbt_project.yml 中同时设置许多资源。