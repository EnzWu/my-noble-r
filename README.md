# my-noble-r
demonstration repo for R installation and agentic use of R

# Use in HSPH BST 260 (Fall 2006)

- start a github codespace
- reference your my-noble-r repository as the basis for the codespace
- wait until the terminal is ready for your input
- `wget https://tinyurl.com/maker260`

After the file is obtained

- `sh -v maker260`

This will take some time (~3 minutes) to install a binary version of R from
Eddelbuettel's r2u system.

Once it completes, test that R is running.

To acquire the RDS serialization of [World Bank developmental indicators](https://datatopics.worldbank.org/world-development-indicators/):

- `wget https://tinyurl.com/worlddevrds`

We will import and investigate this dataset by hand and "agentically". 
