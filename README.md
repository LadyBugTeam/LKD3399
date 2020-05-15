# LKD3399Pro Manual

This repository contains the source files of [LKD3399Pro](https://www.ladybug.com)'s documentation, in MarkDown language.

They are meant to be parsed with the [Sphinx](https://sphinx-doc.org/) documentation builder to build the HTML documentation on [LKD3399Pro's website](https://docs.ladybug.com).


## Building with Sphinx and virtualenv

To build the HTML website (or any other format supported by Sphinx, like PDF, EPUB or LaTeX), you need to install [Sphinx](https://sphinx-doc.org/) >= 1.3 as well as (for the HTML) the [readthedocs.org theme](https://github.com/snide/sphinx_rtd_theme). Only the Python 3 flavor was tested, though the Python 2 versions might work too.

Those tools are best installed using [pip](https://pip.pypa.io), Python's module installer. The Python 3 version might be provided (on Linux distros) by `virtualenv`. You can then run:


```sh
# Install requiremnts
sudo apt-get install virtualenv

virtualenv --python=python3 ./venv
source ./venv/bin/activate
pip install -r requirements.txt

# Build English Manual
make -C en html

# Build Chinese Manual
make -C zh html
```

The compilation might take some time. You can then test the changes live by opening `en/build/html/index.html` in your favorite browser.


`source ./venv/bin/activate` needs to run once before the `make` commands.

## License

All the content of this repository is licensed under the Creative Commons Attribution 3.0 Unported license ([CC BY 3.0](https://creativecommons.org/licenses/by/3.0/)) and is to be attributed to "LadyBug community".
See [LICENSE.txt](/LICENSE.txt) for details.
