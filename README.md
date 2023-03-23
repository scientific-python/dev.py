# 💫 Scientific Python INcantations (`spin`)

## A developer tool for scientific Python libraries

**NOTE:** If you are looking for `devpy`, this is it! We had to rename
the package to publish it on PyPi.

Developers need to memorize a whole bunch of magic command-line incantations.
And these incantations change from time to time!
Typically, their lives are made simpler by a Makefile, but Makefiles can be convoluted, are not written in Python, and are hard to extend.
The rationale behind `spin` is therefore to provide a simple interface for common development tasks.
It comes with a few common build commands out the box, but can easily be customized per project.

As a curiosity: the impetus behind developing the tool was the mass migration of scientific Python libraries (SciPy, scikit-image, and NumPy, etc.) to Meson, after distutils was deprecated.
When many of the build and installation commands changed, it made sense to abstract away the nuisance of having to re-learn them.

## Installation

```
pip install spin
```

## Configuration

Settings are in your project's `pyproject.toml`.
As an example, see the `[tool.spin]` section of [an example `pyproject.toml`](https://github.com/scientific-python/spin/blob/main/example_pkg/pyproject.toml).

The `[tool.spin]` section should contain:

```
package = "pkg_importname"  # name of your package
commands = [
  "spin.cmds.meson.build",
  "spin.cmds.meson.test"
]
```

See [the command selection](#built-in-commands) below.

### Command sections

Once you have several commands, it may be useful to organize them into sections.
In `pyproject.toml`, instead of specifying the commands as a list, use the following structure:

```toml
[tool.spin.commands]
"Build" = [
  "spin.cmds.meson.build",
  "spin.cmds.meson.test"
]
"Environments" = [
  "spin.cmds.meson.shell",
  "spin.cmds.meson.ipython",
  "spin.cmds.meson.python"
]
```

These commands will then be rendered as:

```
Build:
  build  🔧 Build package with Meson/ninja and install
  test   🔧 Run tests

Environments:
  shell    💻 Launch shell with PYTHONPATH set
  ipython  💻 Launch IPython shell with PYTHONPATH set
  python   🐍 Launch Python shell with PYTHONPATH set
```

## Running

```
spin
```

or

```
python -m spin
```

## Built-in commands

### [Meson](https://meson-python.readthedocs.io)

```
  build    🔧 Build package with Meson/ninja and install to `build-install`
  ipython  💻 Launch IPython shell with PYTHONPATH set
  python   🐍 Launch Python shell with PYTHONPATH set
  shell    💻 Launch shell with PYTHONPATH set
  test     🔧 Run pytest
```

### [Build](https://pypa-build.readthedocs.io/en/stable/) (PEP 517 builder)

`spin` was started with Meson in mind, but we're working on expanding commands for PEP 517 `build`.

```
  sdist    📦 Build a source distribution in `dist/`.
```

## 🧪 Custom commands

`spin` can invoke custom commands. These commands define their own arguments, and have access to the `pyproject.toml` file for further configuration.

See, e.g., the [example custom command](https://github.com/scientific-python/spin/blob/main/example_pkg/.spin/cmds.py).

Add custom commands to the `commands` variable in the `[tool.spin]` section of `pyproject.toml` as follows:

```
commands = [..., '.spin/cmds.py:example']
```

Here, the command is stored in `.spin/cmds.py`, and the function
is named `example`.

### Configuration

Custom commands can access the `pyproject.toml` as follows:

```python
from spin import util


@click.command()
def example():
    """Command that accesses `pyproject.toml` configuration"""
    config = util.get_config()
    print(config["tool.spin"])
```

## History

The `dev.py` tool was [proposed for SciPy](https://github.com/scipy/scipy/issues/15489) by Ralf Gommers and [implemented](https://github.com/scipy/scipy/pull/15959) by Sayantika Banik, Eduardo Naufel Schettino, and Ralf Gommers (also see [Sayantika's blog post](https://labs.quansight.org/blog/the-evolution-of-the-scipy-developer-cli)).
Inspired by that implementation, `spin` (this package) is a minimal rewrite by Stéfan van der Walt, that aims to be easily extendable so that it can be used across ecosystem libraries.
We thank Danila Bredikhin and Luca Marconato who kindly donated the `spin` name on PyPi.
