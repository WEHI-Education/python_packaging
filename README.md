# Python Packaging

A lesson on creating and publishing packages in Python, implemented using the
[Carpentries Workbench](https://carpentries.github.io/sandpaper-docs/).

The website can be built locally using:

```R
rmarkdown::pandoc_version()
tmp <- tempfile()
sandpaper::no_package_cache()
sandpaper::create_lesson(tmp, open = FALSE)
sandpaper::build_lesson(tmp, preview = FALSE)
sandpaper::serve()
```

Please consult the Carpentries Workbench docs for info on setting up your R environment.
