# User space

## Summary

Accessing ESMValTool on LUMI is easy, all you need to do is to
add the local module's path to the existing module infrastructure, then just
load the `esmvaltool` module:

```
module use /project/project_462000432/modules/
module load esmvaltool
```

The current module is very basic:

```
~> module show esmvaltool
-------------------------------------------------------------------------------------------------------------------------------------------------
   /project/project_462000432/modules/esmvaltool/2.10.lua:
-------------------------------------------------------------------------------------------------------------------------------------------------
prepend_path("PATH","/project/project_462000432/miniconda-container/bin")
```

since all it needs is a pointer to the containerized conda installation.

Installed software: ESMValTool:`main` (v2.10.0dev state at 9 May 2024), with the following versions:

```
~> esmvaltool version
ESMValCore: 2.10.0
ESMValTool: 2.11.0.dev71+g85e24b535
```

## Running esmvaltool

Once you loaded the module, you can now run `esmvaltool` as any other executable

```
~> which esmvaltool
/project/project_462000432/miniconda-container/bin/esmvaltool
~> esmvaltool --help

```

To be able to run the Tool, you will need a configuration file in either `~/.esmvaltool/config-user.yml` or elsewhere, but
specifiend at command runtime via the `--config_file $PATH` option. A config file ready for running is found in `/project/project_462000432/esmvaltool_configuration_files`.

**NOTE**: there is currently no CMIP/OBS data available, so the Tool will download data in the designated dirs in `config-user.yml`, so be careful
with disk usage!

Once configuration is completed, you can run e.g. an example recipe:

```
esmvaltool run examples/recipe_python.yml
```

# Dev space

## Installation

Installation via containerized conda `conda-containerize`,
based on [LUMI instructions](https://docs.lumi-supercomputer.eu/software/installing/container-wrapper/)


## Steps

```
module load LUMI
module load lumi-container-wrapper
conda-containerize new --mamba --prefix miniconda-container/ ESMValTool_2.10.0dev/environment.yml
export PATH="/project/project_462000432/miniconda-container/bin:$PATH"
cd ESMValTool_2.10.0dev
pip install -e .[develop]
```

## Module loading

Local (to project) built mini-module is avialbale via loading the local project path:

```
module use /project/project_462000432/modules/
```

Then simply loading the module via `module load esmvaltool`.