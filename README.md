# python-censusbatchgeocoder

A simple Python wrapper for [U.S. Census Geocoding Services API batch service](https://www.documentcloud.org/documents/3894452-Census-Geocoding-Services-API.html).

[![Build Status](https://travis-ci.org/datadesk/python-censusbatchgeocoder.png?branch=master)](https://travis-ci.org/datadesk/python-censusbatchgeocoder)
[![PyPI version](https://badge.fury.io/py/censusbatchgeocoder.png)](http://badge.fury.io/py/censusbatchgeocoder)
[![Coverage Status](https://coveralls.io/repos/datadesk/python-censusbatchgeocoder/badge.png?branch=master)](https://coveralls.io/r/datadesk/python-censusbatchgeocoder?branch=master)

* Issues: [github.com/datadesk/python-censusbatchgeocoder/issues](https://github.com/datadesk/python-censusbatchgeocoder/issues)
* Packaging: [pypi.python.org/pypi/censusbatchgeocoder](https://pypi.python.org/pypi/censusbatchgeocoder)
* Testing: [travis-ci.org/datadesk/python-censusbatchgeocoder](https://travis-ci.org/datadesk/python-censusbatchgeocoder)
* Coverage: [coveralls.io/r/datadesk/python-censusbatchgeocoder](https://coveralls.io/r/datadesk/python-censusbatchgeocoder)

### Installation

```bash
$ pip install censusbatchgeocoder
```

## Basic usage

Importing the library

```python
>>> import censusbatchgeocoder
```

According to the [official Census documentation](https://www.documentcloud.org/documents/3894452-Census-Geocoding-Services-API.html), the input is expected to contain the following fields:

* ``id``: Your unique identifier for the record
* ``address``: Structure number and street name (required)
* ``city``: City name (required)
* ``state``: State (optional)
* ``zipcode``: ZIP Code (optional)

You can geocode a comma-delimited file from the filesystem. Results are returned as a list of dictionaries.

An example could look like this:

```text
id,address,city,state,zipcode
1,1600 Pennsylvania Ave NW,Washington,DC,20006
2,202 W. 1st Street,Los Angeles,CA,90012
```

Which is then passed in like this:

```python
>>> results = censusbatchgeocoder.geocode("./my_file.csv")
```

The results are returned with the following columns from the Census

* ``id``: The unique id provided with the record.
* ``returned_address``: The address that was submitted to the geocoder.
* ``geocoded_address``: The address of the match returned by the geocoder.
* ``is_match``: Whether or not the geocoder found a match.
* ``is_exact``: The precision of the match.
* ``coordinates``: The longitude and latitude of the match together in a string.
* ``longitude``: The longitude of the match as a float.
* ``latitude``: The latitude of the match as a float.
* ``tiger_line``: The Census TIGER line of the match.
* ``side``: The side of the Census TIGER line of the match.
* ``state_fips``: The FIPS state code identifying the state of the match.
* ``county_fips``: The FIPS county code identifying the county of the match.
* ``tract``: The Census tract of the match.
* ``block``: The Census block of the match.

```python
>>> print results
[{u'address': u'1600 Pennsylvania Ave NW',
  u'block': u'1031',
  u'city': u'Washington',
  u'coordinates': u'-77.03535,38.898754',
  u'county_fips': u'001',
  u'geocoded_address': u'1600 Pennsylvania Ave NW, Washington, DC, 20006',
  u'id': u'1',
  u'is_exact': u'Non_Exact',
  u'is_match': u'Match',
  u'latitude': 38.898754,
  u'longitude': -77.03535,
  u'returned_address': u'1600 PENNSYLVANIA AVE NW, WASHINGTON, DC, 20502',
  u'side': u'L',
  u'state': u'DC',
  u'state_fips': u'11',
  u'tiger_line': u'76225813',
  u'tract': u'006202',
  u'zipcode': u'20006'},
 {u'address': u'202 W. 1st Street',
  u'block': u'1034',
  u'city': u'Los Angeles',
  u'coordinates': u'-118.24456,34.053005',
  u'county_fips': u'037',
  u'geocoded_address': u'202 W. 1st Street, Los Angeles, CA, 90012',
  u'id': u'2',
  u'is_exact': u'Exact',
  u'is_match': u'Match',
  u'latitude': 34.053005,
  u'longitude': -118.24456,
  u'returned_address': u'202 W 1ST ST, LOS ANGELES, CA, 90012',
  u'side': u'L',
  u'state': u'CA',
  u'state_fips': u'06',
  u'tiger_line': u'141618115',
  u'tract': u'207400',
  u'zipcode': u'90012'}]
```

Any extra metadata fields included in the file are still present in the returned data.

So the ``my_metadata`` column here...

```text
id,address,city,state,zipcode,my_metadata
1,1600 Pennsylvania Ave NW,Washington,DC,20006,foo
2,202 W. 1st Street,Los Angeles,CA,90012,bar
```

.. is still there after you geocode.

```python
>>> censusbatchgeocoder.geocode("./my_file.csv")
[{u'address': u'1600 Pennsylvania Ave NW',
  u'block': u'1031',
  u'city': u'Washington',
  u'coordinates': u'-77.03535,38.898754',
  u'county_fips': u'001',
  u'geocoded_address': u'1600 Pennsylvania Ave NW, Washington, DC, 20006',
  u'id': u'1',
  u'is_exact': u'Non_Exact',
  u'is_match': u'Match',
  u'latitude': 38.898754,
  u'longitude': -77.03535,
  u'returned_address': u'1600 PENNSYLVANIA AVE NW, WASHINGTON, DC, 20502',
  u'my_metadata': 'foo',
  u'side': u'L',
  u'state': u'DC',
  u'state_fips': u'11',
  u'tiger_line': u'76225813',
  u'tract': u'006202',
  u'zipcode': u'20006'},
 {u'address': u'202 W. 1st Street',
  u'block': u'1034',
  u'city': u'Los Angeles',
  u'coordinates': u'-118.24456,34.053005',
  u'county_fips': u'037',
  u'geocoded_address': u'202 W. 1st Street, Los Angeles, CA, 90012',
  u'id': u'2',
  u'is_exact': u'Exact',
  u'is_match': u'Match',
  u'latitude': 34.053005,
  u'longitude': -118.24456,
  u'returned_address': u'202 W 1ST ST, LOS ANGELES, CA, 90012',
  u'my_metadata': 'foo',
  u'side': u'L',
  u'state': u'CA',
  u'state_fips': u'06',
  u'tiger_line': u'141618115',
  u'tract': u'207400',
  u'zipcode': u'90012'}]
```

#### Custom column names

If you have column headers that do not exactly match those expected by the geocoder you should override them.

So a file like this:

```text
foo,bar,baz,bada,boom
1,521 SWARTHMORE AVENUE,PACIFIC PALISADES,CA,90272-4350
2,2015 W TEMPLE STREET,LOS ANGELES,CA,90026-4913
```

Can be mapped like this:

```python
>>> censusbatchgeocoder.geocode(
    self.weird_path,
    id="foo",
    address="bar",
    city="baz",
    state="bada",
    zipcode="boom"
)
```

#### Optional columns

The state and ZIP Code columns are optional. If your data doesn't have them, pass ``None`` as keyword arguments.

```python
>>> censusbatchgeocoder.geocode("./my_file.csv", state=None, zipcode=None)
```

#### Lists of dictionaries

A list of dictionaries, like those created by the csv module's ``DictReader`` can also be mapped.

```python
>>> my_list = [{'address': '521 SWARTHMORE AVENUE',
  'city': 'PACIFIC PALISADES',
  'id': '1',
  'state': 'CA',
  'zipcode': '90272-4350'},
 {'address': '2015 W TEMPLE STREET',
  'city': 'LOS ANGELES',
  'id': '2',
  'state': 'CA',
  'zipcode': '90026-4913'}]
>>> censusbatchgeocoder.geocode(my_list)
```

#### pandas DataFrames

You can geocode a pandas DataFrame by converting it into a list of dictionaries.

```python
>>> result = censusbatchgeocoder.geocode(df.to_dict("records"))
```

Then convert it back into a DataFrame.

```python
>>> result_df = pd.DataFrame(result)
```

That's it.

#### File objects

You can also geocode an in-memory file object of data in CSV format.

```python
>>> my_data = """id,address,city,state,zipcode
1,1600 Pennsylvania Ave NW,Washington,DC,20006
2,202 W. 1st Street,Los Angeles,CA,90012"""
>>> censusbatchgeocoder.geocode(io.StringIO(my_data))
```

#### Different encodings

If you are using Python 2 and your CSV file has an unusual encoding that's causing problems, try explicitly passing in the encoding name.

```python
>>> censusbatchgeocoder.geocode("./my_file.csv", encoding="utf-8-sig")
```
