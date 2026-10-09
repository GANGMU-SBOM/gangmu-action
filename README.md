# gangmu-action

在 GitHub Actions 里一行接入 [gangmu](https://github.com/GANGMU-SBOM/gangmu)：给嵌入式 C/C++ 固件生成 SBOM 和密码物料清单（CBOM），并写出一份后量子迁移摘要。English: [README.en.md](README.en.md)。

```yaml
- uses: actions/checkout@v4
- uses: GANGMU-SBOM/gangmu-action@main
  with:
    compile-db: build/compile_commands.json   # 可选，但建议给
    fail-on: quantum-vulnerable               # 可选，不写就不因算法失败
```

跑完得到三个文件（默认放在 `gangmu-out/`，并作为 artifact 上传）：

| 文件 | 内容 |
| --- | --- |
| `sbom.cdx.json` | CycloneDX SBOM |
| `cbom.cdx.json` | CycloneDX 1.6 密码物料清单 |
| `pqc-readiness.md` | 后量子迁移摘要，同时写进该次运行的 Summary 页 |

## 输入

| 名称 | 默认 | 说明 |
| --- | --- | --- |
| `path` | `.` | 要扫描的目录 |
| `compile-db` | | 真实构建产出的 `compile_commands.json`。不给的话 CBOM 列的是“树里有什么”，不是“编出来什么” |
| `link-map` | | 真实构建的链接 map |
| `sbom` / `cbom` | `true` | 是否生成 SBOM / CBOM 与摘要 |
| `fail-on` | | 空格分隔：`quantum-vulnerable`、`legacy`。命中就让 job 失败；报告照样先写完、先上传 |
| `output-dir` | `gangmu-out` | 报告目录 |
| `upload` / `artifact-name` | `true` / `gangmu-reports` | 是否上传，以及名字 |
| `install-spec` | `gangmu-sbom` | pip 安装什么。要可复现就写 `gangmu-sbom==版本` |
| `extra-packages` | | 额外安装的 pip 包，例如 CBOM 规则包 |
| `python-version` | `3.12` | |

输出：`output-dir`、`sbom-file`、`cbom-file`、`readiness-file`。

## 注意

* 后量子摘要需要 gangmu 0.9 或更高。旧版本会跳过摘要并给出警告，SBOM 和 CBOM 照常生成。
* 目前 PyPI 上的最新版是 0.8.0；要用 0.9 的功能，先用 `install-spec: git+https://github.com/GANGMU-SBOM/gangmu.git@main`，0.9.0 发布后改回默认。
* 输入值只通过环境变量传给脚本，不会被拼进 shell 命令。
* 识别靠名字，不分析密钥长度和协议；详见 gangmu 的 [CBOM 指南](https://github.com/GANGMU-SBOM/gangmu/blob/main/docs/guides/cbom-post-quantum.md)。

## 许可

Apache-2.0，见 `LICENSE`。
