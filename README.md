# Python Packaging

A lesson on creating and publishing packages in Python, implemented using the
[Carpentries Workbench](https://carpentries.github.io/sandpaper-docs/).

The website can be built locally using:

Install R deps:
```R
> install.packages(c("sandpaper", "varnish", "pegboard"),
  repos = c("https://carpentries.r-universe.dev/", getOption("repos")))
```

Build and serve website:
```bash
$ Rscript build_script.R
```

Please consult the Carpentries Workbench docs for info on setting up your R environment.

For `vscode`, install the `R Extension for Visual Studio Code` plugin and install the `languageserver` to get syntax highlighting in R Markdown documents.