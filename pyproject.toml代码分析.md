这个 `pyproject.toml` 不是“业务代码”，而是 Biomni 这个 Python 项目的 **构建、打包、依赖和开发工具配置文件**。它定义了项目怎么被安装、需要哪些基础依赖、从哪里取版本号、哪些包要被包含，以及代码风格检查怎么运行。([GitHub][1])

我按模块拆开说明。

## 1. 构建系统

文件开头的 `[build-system]` 指定：

* 使用 `setuptools>=61.0` 和 `wheel`
* 构建后端是 `setuptools.build_meta` ([GitHub][1])

这表示 Biomni 采用的是现在很常见的 **PEP 517/518 标准 setuptools 打包方式**。也就是说，当你执行安装或构建时，Python 会按这里指定的方式来生成包，而不是依赖旧式 `setup.py` 直调。([GitHub][1])

## 2. 项目基础元信息

`[project]` 这一段定义了 Biomni 的基本身份信息：

* 项目名是 `biomni`
* 版本号不是写死的，而是动态获取
* 描述是 `"Biomni"`
* README 文件是 `README.md`
* 许可证是 `Apache-2.0`
* 许可证文件是 `LICENSE`
* 作者信息写的是 `Biomni Team`，邮箱是 `kexinh@cs.stanford.edu`
* 要求 Python 版本 `>=3.11` ([GitHub][1])

这说明几个关键点：

第一，**这个项目至少要求 Python 3.11**，低于 3.11 的环境不在支持范围内。
第二，它是按标准 Python 包元数据方式发布的，适合直接做 pip 安装。
第三，虽然你看的仓库是 fork 版，但这里的主页和仓库 URL 仍然指向 `snap-stanford/biomni`，说明元信息还沿用上游源仓库。([GitHub][1])

## 3. 运行时依赖

`dependencies` 只列了 3 个基础依赖：

* `pydantic`
* `langchain`
* `python-dotenv` ([GitHub][1])

这说明 `pyproject.toml` 里声明的只是 **最核心、最小化的安装依赖**，而不是把所有可选生态一次性全装上。结合你前面看的代码，这很合理，因为 Biomni 许多能力是按模块扩展的，很多工具依赖可能没有全部写进这里，而是需要按具体功能额外安装。这个判断是基于前面代码中大量使用了更多第三方库，而 `pyproject.toml` 里只保留了最基础三项。([GitHub][1])

## 4. 项目链接

`[project.urls]` 里定义了：

* Homepage = `https://github.com/snap-stanford/biomni`
* Repository = `https://github.com/snap-stanford/biomni` ([GitHub][1])

这主要是给 PyPI 或安装工具展示项目主页和源码地址用的。
值得注意的是，它没有改成你这个 fork 的地址，而是仍指向上游仓库。([GitHub][1])

## 5. 可选依赖

`[project.optional-dependencies]` 里目前只定义了一个额外依赖组：

* `gradio = ["gradio>=5.0,<6.0"]` ([GitHub][1])

这表示如果用户需要 Biomni 的 Gradio 相关界面能力，可以按 extras 方式安装；例如概念上会是装 `biomni[gradio]`。这是一种把 UI 依赖与核心依赖分开的做法，避免所有用户都被迫安装较重的界面组件。([GitHub][1])

## 6. setuptools 打包配置

`[tool.setuptools]` 和后面的几段，决定了 Biomni 包是怎么被发现和打包的：

* `include-package-data = true`：打包时包含包数据文件
* `version = {attr = "biomni.version.__version__"}`：版本号从 `biomni.version.__version__` 动态读取
* `[tool.setuptools.packages.find]` 中 `exclude = ["test*", "tutorials*"]`：自动找包时排除测试和教程目录 ([GitHub][1])

这里有几个关键信号：

第一，**版本号集中维护在 Python 模块里**，而不是在 `pyproject.toml` 里手写。
第二，项目打包时会包含额外的数据文件，这对 Biomni 很重要，因为你前面看到它有不少协议文本、schema、数据资源等非纯代码文件。
第三，测试和教程不会作为正式安装包的一部分。([GitHub][1])

## 7. Ruff 代码检查与格式化配置

后半部分是 `[tool.ruff]` 配置，这说明 Biomni 使用 **Ruff** 作为主要的 lint/格式化工具。配置里包括：

* `src = ["src"]`
* `line-length = 120`
* `docstring-code-format = true` ([GitHub][1])

以及 `[tool.ruff.lint]` 中启用的规则集：

* `F`：Pyflakes
* `E`、`W`：Pycodestyle
* `I`：isort
* `B`：flake8-bugbear
* `TID`：flake8-tidy-imports
* `C4`：flake8-comprehensions
* `BLE`：blind-except
* `UP`：pyupgrade
* `RUF100`：unused noqa
* `TCH`：typing imports ([GitHub][1])

同时它还显式忽略了 `E501`，也就是“行太长”。注释里写得很清楚：作者接受较长的注释行，长代码行交给格式化器处理。([GitHub][1])

这部分反映出项目的开发规范：

* 倾向使用自动化 lint
* 接受较宽的 120 列行宽
* 对 import 排序、异常捕获、类型导入、现代 Python 语法升级都比较重视
* 没有启用更严格的文档或 pylint 规则，因为对应项被注释掉了 ([GitHub][1])

## 8. 这个文件在整个项目里的实际作用

综合来看，这个 `pyproject.toml` 实现的功能不是算法功能，而是项目工程层面的 4 件事：

* 规定 **怎么构建和安装** Biomni
* 声明 **最核心依赖** 和 **可选界面依赖**
* 指定 **版本号和打包范围**
* 统一 **代码检查与格式化规则** ([GitHub][1])

## 9. 两个值得注意的点

第一，`[tool.ruff]` 里写的是 `src = ["src"]`，但 Biomni 仓库本身你前面看的实际代码主要在 `biomni/` 目录下，而不是典型的 `src/` 布局。就这份配置本身来看，这里可能是沿用模板、历史遗留，或者项目部分阶段使用过 `src` 布局。仅凭这个文件，我不能确定它当前是否完全匹配仓库结构。([GitHub][1])

第二，这份 `pyproject.toml` 声明的依赖明显少于项目实际代码里用到的库数量，所以它更像“核心最小安装配置”，而不是“装完即可覆盖全部功能”的完整依赖清单。这一点和你前面看到的 `arxiv`、`scholarly`、`requests`、`PyPDF2`、`Bio` 等广泛使用情况形成对照。这个是基于仓库代码和该文件内容做出的推断。([GitHub][1])

一句话总结：
这个 `pyproject.toml` 的功能是把 Biomni 定义成一个基于 setuptools 的 Python 3.11+ 包，声明核心依赖与可选 Gradio 依赖、动态版本来源、打包排除规则，以及 Ruff 的 lint/format 规范。([GitHub][1])

如果你愿意，我下一步可以继续帮你把这个 `pyproject.toml` 逐行翻译成中文注释版。

[1]: https://github.com/pengsihua2023/Biomni/blob/main/pyproject.toml "Biomni/pyproject.toml at main · pengsihua2023/Biomni · GitHub"
