# User Documentation Proof of Concept with Read the Docs

This is a proof of concept for using Read the Docs to setup a user documentation site backed by GitHub source control with versioning and template rendering. 

[Read the Docs](https://readthedocs.org/) is a set of software documentation tools for building, rendering, hosting, and managing documentation, all for free. It provides many of the features we need out of the box, such as versioning and template rendering. 

Read the Docs works natively with two underlying documentation generation engines, [Sphinx](https://docs.readthedocs.io/en/stable/intro/getting-started-with-sphinx.html) and [MkDocs](https://docs.readthedocs.io/en/stable/intro/getting-started-with-mkdocs.html). Both engines have pros and cons, but this proof of concept uses MkDocs, as it is more geared toward markdown content than Sphinx. 

## GitHub Setup

To begin, we created a new GitHub repository to host our documentation. The repository contains the following top level files and directories:

* `docs/` - This directory hosts the documentation content. It must be a distinct directory from the directory which contains the configuration files (the top level, in this case), though they can be nested. 
* `README` - A standard GitHub repo README with information about the repository contents. Not used by Read the Docs in this proof of concept. 
* `.readthedocs.yml` - A [configuration file](https://docs.readthedocs.io/en/stable/config-file/index.html) for Read the Docs. It specifies the documentation generation engine to be used. It can further configure Read the Docs, but it's pretty barebones for the proof of concept. 
* `mkdocs.yml` - A [configuration file](https://www.mkdocs.org/user-guide/configuration/) for MkDocs. Specifies the theme, the navigation layout, and other metadata for the documentation in our proof of concept. 

The `docs/` directory is also populated with markdown files for the documentation content. 

## Read the Docs Setup

With the GitHub repository ready, we set up an account on Read the Docs and imported our documentation from GitHub. For the proof of concept, the account was set up with a personal GitHub account. For a full implementation, a more robust authorization system should be considered. Read the Docs provides organization management through their [For Business](https://docs.readthedocs.io/en/stable/commercial/organizations.html) offering, which currently costs $50/mo. for the basic plan. 

While signed in with a new Read the Docs account, we clicked Import Project on the projects page and selected the GitHub repository created earlier from the list of available repositories. Read the Docs automatically imported our documentation and set up our documentation site (though it took some fiddling with configuration and formatting to get it completely correct; the end product can be seen in the repository and Read the Docs admin panel). Read the Docs performs documentation builds automatically, so we did not have to integrate with our CI system at all. 

## Versioning

By default, Read the Docs builds a latest version from the `master` branch in GitHub. To test versioning, we created a couple of GitHub releases based on different commits in the repository. Then, in the Read the Docs administration dashboard, we activated new versions based on the GitHub releases, `v0.0.1` and `v.0.0.2`. We also changed the default version to point to `v0.0.2` via the Admin > Advanced Settings panel. Unforuntately, it does not seem like versioning can be configured using configuration files. 

## Interactivity

One of our major goals for documentation is that it provide an interactive learning experience. We have identified [Jupyter](https://jupyter.org/) as a possible tool for providing interactivity. Although Read the Docs does not provide Jupyter integration out of the box, there are other options for integration. A promising solution is to use [Jupyter Widgets](https://github.com/jupyter-widgets/ipywidgets), which are able to [embed in static web pages](https://ipywidgets.readthedocs.io/en/latest/embedding.html). However, while working with Read the Docs, we can't just embed widgets to a static html page, because Read the Docs uses [Sphinx](https://docs.readthedocs.io/en/stable/intro/getting-started-with-sphinx.html) and [MkDocs](https://docs.readthedocs.io/en/stable/intro/getting-started-with-mkdocs.html) for building the documentation. MkDocs works with the Markdown file format and Sphinx was designed to work with reStructuredText, but Markdown can be used as well by providing this [extension](https://recommonmark.readthedocs.io/en/latest). 

There is no direct Jupyter integration for Sphinx or MkDocs, however, there are some plugins that could be used to get close to what we want. Our available options are:

* **Using [mkdocs-jupyter](https://github.com/danielfrg/mkdocs-jupyter) plugin with MkDocs**: We can add a pre-executed Jupyter notebook to our documentation. In order to do so, `mkdocs-jupyter` has to specified as a plugin in `mkdocs.yml`. Since this feature is dependent on a plugin, this dependency needs to be installed in the Read the Docs environment as well. So, a `requirements.txt` file is created which contains the package name(`mkdocs-jupyter`) and the path to the requirements file is specificed in `.readthedocs.yml`. As of now the path from root of the repository is `requirements/requiremnts.txt`. Notebooks that need to be added to the documentattion are put in `docs/`. Notebooks are added to the navigation by specifiying in the `mkdocs.yml` file, similar to as we do for Markdown files. An [example notebook](https://crate-docs-mkdocs.readthedocs.io/en/latest/examplenotebook/) has been added for the POC.
* **Using [jupyter-sphinx](https://jupyter-sphinx.readthedocs.io/en/latest/) extension with Sphinx**: Sphinx is more versatile than MkDocs and with the help of [jupyter-sphinx](https://jupyter-sphinx.readthedocs.io/en/latest/) extension, Jupyter widgets can be embedded within the resulting documentation. More information on using Sphinx and `jupyter-sphinx` can be found [here](https://crate-docs-mkdocs.readthedocs.io/en/latest/examplenotebook/).


				
## Future Work

This proof of concept is just a start. It demonstrates a bare bones Read the Docs setup with basic configuration and versioning. The following additional work is necessary to get a fully featured user documentation site up and running:

* Setup a [custom domain name](https://docs.readthedocs.io/en/stable/custom_domains.html). 
* Create a [custom theme](https://www.mkdocs.org/user-guide/styling-your-docs/). This will likely need to be stored in its own repository. 
* Determine proper administration of the documentation. 
  * Whose [GitHub account](https://docs.readthedocs.io/en/stable/connected-accounts.html) owns it? 
  * Do we need the additional management features of [Read the Docs for Business](https://docs.readthedocs.io/en/stable/commercial/index.html)?
* Add interactive components, per the above section. 
* Write documentation content. 
