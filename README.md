## Data Collection and ETL: ACS Blockgroup Data by States

* [1 The Goals of the Repo](#1-the-goals-of-the-repo)
* [2 Repo Structure](#2-repo-structure)
  + [2.1 fast.ai `nbdev` framework](#21-fastai--nbdev--framework)
  + [2.2 Directory Structure](#22-directory-structure)
* [3 Data Collection and ETL](#3-data-collection-and-etl)
  + [3.1 Data Collection](#31-data-collection)
  + [3.2 Basic Data Cleaning](#32-basic-data-cleaning)
  + [3.3 Data Loading](#33-data-loading)
* [4 Unsolved Problems and Possible Future Developments](#4-unsolved-problems-and-possible-future-developments)

### 1 The Goals of the Repo

* Pull data from American Community Survey database using open APIs.
* Perform basic transformation and data cleaning process on the data.
* Load the data into the database.

### 2 Repo Structure

#### 2.1 fast.ai `nbdev` framework

The `nbdev` framework helps users to compose custom libraries within Jupyter Notebook environment and subsequently converts scripts into python modules. It allows users to see intermediate results of the codes while maintaining relatively robust library structure.

#### 2.2 Directory Structure

This demo contains mainly two modules, which are separated into two notebooks in the `./nbs` folder. Viewers may review and modify the notebooks if needed. 

Converted modules are stored within the `./acs_etl` folder, with the formal name of the library, which should not be modified.

After every modification, run `nbdev_build_lib` to "commit" changes to the converted modules.

### 3 Data Collection and ETL

#### 3.1 Data Collection

In the data collection process, the repo uses [`census`](https://github.com/datamade/census) API by [datamade](https://github.com/datamade) to compose request URL and fetch data from the original source. 

`census` is a well designed wrapper for the United States Census Bureau's API. It simplifies the operation of composing request URL and parsing data, making it easy for end user to access the survey data.

As an example, users may create a `Census` object by specifying the API key and the querying year, then call the following commend to obtain the respond from the original API.

```python
from census import Census
from us import states

c = Census("MY_API_KEY")
c.acs5.get(('NAME', 'B25034_010E'),
          {'for': 'state:{}'.format(states.MD.fips)})
```

Viewers may consult [the repo](https://github.com/datamade/census) for census API for detailed instructions.

#### 3.2 Basic Data Cleaning

In the module `acs_etl.acs`, a basic data cleaning process is performed to transform the data into an ideal format.

Specifically, variables are renamed by meaningful terms and state names and county names are generated from the `NAME` column, while the original `state` and `county` columns are transformed into `state_id` and `county_id`, which can be used as identifiers for observations.

#### 3.3 Data Loading

In the `acs_etl.load` module, the fetched and transformed data is loaded to the database using primarily the `psycopg2` package, which provides an interface for users to interact with PostgreSQL databases using Python.

When interacting with the database, a custom decorator is design to control the connection session and the cursor, making sure they are closed after used or failures/exceptions. 

The two methods in the class `ACS_Blockgrou_Data_Loader` are decorated by the decorator. These two methods allow user to execute customized SQL command through the database connection and with the data. A simple test case is enclosed below to demonstrate the connecting logic.

### 4 Unsolved Problems and Possible Future Developments

* Higher level of security setting in database connection could be implemented, such as using SSH tunnel based on the `sshtunnel` package.
* The data loading class should provide higher level of customization with regards to multiple parameters, such as database settings, table constraints etc.
* ...

