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


Installed software: ESMValTool:`v2.10.x` branch (v2.10 stable tag), with the following versions:

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
git clone https://github.com/ESMValGroup/ESMValTool.git
conda-containerize new --mamba --prefix miniconda-container/ ESMValTool/environment.yml
```

Once that completed, prepend the new path:

```
export PATH="/project/project_462000432/miniconda-container/bin:$PATH"
```

(this is somwhat equivalent to `conda activate esmvaltool`), and now you can use the containerized `pip`
to install esmvaltool in user editable, development mode:

```
cd ESMValTool
pip install -e .[develop]
```

This installation will be placed in your `$HOME/.local` dir and will be sitting on top of the container;
be advised that your `$HOME` dir is not readable by other users, and on LUMI you can not open it for
group reads.

# Dev space

## Module loading

Local (to project) built mini-module is avialbale via loading the local project path:

```
module use /project/project_462000432/modules/
```

Then simply loading the module via `module load esmvaltool`.


## ESMValTool build on `project_462000432`

`esmvaltool` is built as a pure conda package; this means all dependencies are installed inside the conda container e.g.:

```
python -c "import esmvalcore; print(esmvalcore.__file__)"
/LUMI_TYKKY_D1Npoag/miniconda/envs/env1/lib/python3.11/site-packages/esmvalcore/__init__.py

python -c "import esmvaltool; print(esmvaltool.__file__)"
/LUMI_TYKKY_D1Npoag/miniconda/envs/env1/lib/python3.11/site-packages/esmvaltool/__init__.py
```

Build steps:

- create an `env_210.yml` to hold just `esmvaltool` as dependency
- run the containerized conda env builder: `conda-containerize new --mamba --prefix miniconda-container env_210.yml`
- export path: `export PATH="/project/project_462000432/miniconda-container/bin:$PATH"`
- make sure no leftover local pip installs in `$HOME/.local`

Updating an environment via a command can be done with: `conda-containerize update miniconda-container --post-install postinstall_esmvaltool`
with `postinstall_esmvaltool` a file that may contain eg `mamba install -c conda-forge some-package`; note that this may result in conflicts, just as normal conda was used!

## Resources

- [Python packages on LUMI](https://docs.lumi-supercomputer.eu/software/installing/python/)
- [Containerized Conda on LUMI](https://docs.lumi-supercomputer.eu/software/installing/container-wrapper/)
- [Modules on LUMI](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/Lmod_modules)
