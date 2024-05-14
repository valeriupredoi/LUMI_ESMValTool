# User space

## Project-wide ESMValTool

Accessing ESMValTool on LUMI is easy, if you are a member of project `project_462000432`, and have
access to `/project/project_462000432/`. All you need to do is to
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


Installed software: ESMValTool:`main` (v2.10 stable tag), with the following versions:

```
~> esmvaltool version
ESMValCore: 2.10.0
ESMValTool: 2.10.0
```

The installation is pure conda-forge. You can use the `conda-list` executable to examine the dependency environment.

## Running esmvaltool

Once you loaded the module, you can now run `esmvaltool` as any other executable

```
~> which esmvaltool
/project/project_462000432/miniconda-container/bin/esmvaltool
~> esmvaltool --help

```

### User configuration file (`config-user.yml`)

To be able to run the Tool, you will need a configuration file in either `~/.esmvaltool/config-user.yml` or elsewhere, but
specifiend at command runtime via the `--config_file $PATH` option. A config file ready for running is found in `/project/project_462000432/esmvaltool_configuration_files`. You can create your own `~/.esmvaltool` directory, and put that file in there.

**NOTE**: there is currently no CMIP/OBS data available, so the Tool will download data in the designated dirs in `config-user.yml`, so be careful
with disk usage!

Once configuration is completed, you can run e.g. an example recipe:

```
esmvaltool run examples/recipe_python.yml
```

## User editable installation of ESMValTool

You can use the provided `conda-containerized` infrastructure to install your own copy of ESMValTool;
such installation would be in editable, development mode, so you can perform changes inside the ESMValTool's
source code, for development purposes. Installation uses containerized conda `conda-containerize`,
based on [LUMI instructions](https://docs.lumi-supercomputer.eu/software/installing/container-wrapper/)

First, load the relevant LUMI modules:

```
module load LUMI
module load lumi-container-wrapper
```

Then `mkdir miniconda-container` where the environment will be linked to,
the create the containerized conda environment based on ESMValTool's `environment.yml`:

```
conda-containerize new --mamba --prefix miniconda-container/ ESMValTool/environment.yml
```

Once that completed, prepend the new path:

```
export PATH="/project/project_462000432/miniconda-container/bin:$PATH"
```

(this is somwhat equivalent to `conda activate esmvaltool`), and now you can use the containerized `pip`
to install esmvaltool in user editable, development mode:

```
cd ESMValTool_2.10.0dev
pip install -e .[develop]
```

This installation will be placed in your `$HOME/.local` dir and will be sitting on top of the container;
be advised that your `$HOME` dir is not readable by other users, and on LUMI you can not open it for
group reads.

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

## ESMValTool build

`esmvaltool` is built as a local Python package using the containerized `pip`, via `pip install -e .[develop]`;
this means all dependencies are installed inside the conda container e.g.:

```
python -c "import esmvalcore; print(esmvalcore.__file__)"
/LUMI_TYKKY_Buq2VlS/miniconda/envs/env1/lib/python3.11/site-packages/esmvalcore/__init__.py
```

but `esmvaltool` is actually built locally, on top of the container:

```
python -c "import esmvaltool; print(esmvaltool.__file__)"
/pfs/lustrep1/projappl/project_462000432/ESMValTool_2.10.0dev/esmvaltool/__init__.py
```

This should be OK, since LUMI allow this for fairly simpl(er) packages.

## Resources

- [Python packages on LUMI](https://docs.lumi-supercomputer.eu/software/installing/python/)
- [Containerized Conda on LUMI](https://docs.lumi-supercomputer.eu/software/installing/container-wrapper/)
- [Modules on LUMI](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/Lmod_modules)
