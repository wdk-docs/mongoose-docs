# 如何使用

## 安装 mkdocs

```sh
pip install mkdocs
mkdocs new my-project
cd my-project
mkdocs serve
```

## 安装 theme

```sh
pip install mkdocs-material
```

## 安装扩展

```sh
pip install pymdown-extensions
```

## 错误处理

TypeError: unhashable type: 'dict'

注释掉配置文件中

```
logo:
    icon: "\uE80C"
```

为

```
icon:
    logo: material/library
```

jinja2.exceptions.TemplateNotFound: .icons/.svg

````
extra:
  social:
    - type: globe
      link: http://wdk-docs.github.io
    - type: github-alt
      link: https://github.com/wdk-docs
    - type: twitter
      link: https://twitter.com/wdk-docs
    - type: linkedin
      link: https://linkedin.com/in/wdk-docs
      ```
````

为

```
extra:
  social:
    - icon: fontawesome/brands/github
      link: http://wdk-docs.github.io
    - icon: fontawesome/brands/github-alt
      link: https://github.com/wdk-docs
    - icon: fontawesome/brands/twitter
      link: https://twitter.com/wdk-docs
    - icon: fontawesome/brands/linkedin
      link: https://linkedin.com/in/wdk-docs
```
