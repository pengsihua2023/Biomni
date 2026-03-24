这个 `MANIFEST.in` 的功能是：**控制源码分发包（sdist）里要包含哪些文件、排除哪些文件**。它不是运行时代码，也不定义 Python 依赖；它主要影响你执行打包发布时，哪些非 `.py` 文件会一起进入发布包。([GitHub][1])

按内容拆开看：

`recursive-include biomni *.py`
这句表示把 `biomni` 包下面的所有 Python 源文件递归包含进打包结果。也就是无论子目录多深，只要是 `*.py`，都会被纳入源码包。([GitHub][1])

`recursive-include biomni/tool/schema_db *.pkl`
这句专门把 `biomni/tool/schema_db` 目录下的 `.pkl` 文件打包进去。结合你前面看的 `schema_db` 目录，这一步很重要，因为那些 `.pkl` 是数据库/API schema 资源，如果不打进包里，相关数据库查询功能可能无法正常读取这些 schema 文件。([GitHub][1])

`recursive-include biomni_env *.py *.sh *.yml *.yaml *.txt *.md *.json *.R`
这句表示把 `biomni_env` 目录下多种类型的文件都包含进去，包括 Python、Shell、YAML、文本、Markdown、JSON 和 R 脚本。说明作者希望把这个环境目录下的多种辅助资源一起发布，而不只是 Python 代码。([GitHub][1])

接下来是两个排除目录的规则：

* `prune tutorials/data`
* `prune biomni_env/biomni_tools`

这表示这两个目录即使存在匹配文件，也不要放进源码包。也就是说，教程数据和 `biomni_env` 里的 `biomni_tools` 子目录被明确排除在打包之外。([GitHub][1])

然后是一些全局排除规则：

* `global-exclude *.py[co]`
* `global-exclude __pycache__`
* `global-exclude .ruff_cache`
* `prune build`
* `prune dist`
* `prune *.egg-info`

这些规则的作用是把常见的构建产物和缓存文件排除掉，比如 `.pyc/.pyo`、`__pycache__`、Ruff 缓存、`build/`、`dist/` 和 `egg-info`。这属于标准的“不要把临时文件和构建垃圾带进发布包”的做法。([GitHub][1])

最后三句：

* `include README.md`
* `include LICENSE`
* `include pyproject.toml`

表示把 README、许可证和 `pyproject.toml` 明确加入源码包。这通常是为了保证发布包里带有项目说明、许可信息和构建配置。([GitHub][1])

结合你刚才看的 `pyproject.toml`，这两个文件分工不同：

* `pyproject.toml` 负责定义项目元信息、依赖、构建系统和开发工具配置。
* `MANIFEST.in` 负责决定源码分发包里具体装哪些文件。([GitHub][1])

对 Biomni 这个项目来说，这个 `MANIFEST.in` 的一个关键作用，是**确保非代码资源也能跟着一起发布**，尤其是 `biomni/tool/schema_db/*.pkl` 这类运行时要读取的资源文件。否则仅靠打包 Python 文件，很多数据库查询功能可能会缺资源。这个判断是由该文件显式包含 `.pkl` 规则得出的。([GitHub][1])

一句话总结：
这个 `MANIFEST.in` 实现的是 Biomni 源码包的文件清单控制，核心是**包含 `biomni` 代码和关键资源文件，包含 `biomni_env` 中的多种辅助文件，同时排除教程数据、缓存和构建产物**。([GitHub][1])

你要是愿意，我下一步可以继续帮你分析 `setup.py` 或 `biomni/version.py` 这类和打包发布直接相关的文件。

[1]: https://raw.githubusercontent.com/pengsihua2023/Biomni/main/MANIFEST.in "raw.githubusercontent.com"
