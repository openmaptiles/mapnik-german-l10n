## Software requirements:

* GNU/Linux OS
* Postgresql 8.4 or 8.5, PostGIS 2.x
* Kanji Kana Simple Inverter library (http://kakasi.namazu.org/)
* ICU - International Components for Unicode library (http://site.icu-project.org/)

This code is developed on Debian 9.x and should also work on Debian
derivatives like Ubuntu and other GNU/Linux distributions.

All required libraries can be installed from their respective repositories.

Microsoft Windows is currently not supported and I have no plans to do so.
If you feel an urgend need to port this code to Windows I would be happy to
take patches.

To install the l10n into your database the following steps are requered:

### 1. Install the libraries for the C/C++ stored procedures


The easiest way to to this on Debian/Ubuntu is to build packages and install
them:

```sh
make deb
```

To make this work you will need to install the required libraries:

sudo apt-get install devscripts equivs
sudo mk-build-deps -i debian/control

On other Distributions it should work to use `make`/`make install`, given the
required libraries listed in `debian/control` have been installed.
I would be happy if somebody would contribute a spec-file for rpm based
distributions.

The build process will need to download country_osm_grid.sql from
http://www.nominatim.org/data/country_grid.sql.gz
If your computer is offline for some reason. Just download this file and
put it inside your build directory.

Thai transcript is a seperate extension because it is based on python
(https://github.com/PyThaiNLP/pythainlp) and installing
postgresql-plpython3 is probably not an option for anybody.

If osml10n_thai_transcript is not installed transcription for thai language
will fall back to libicu which will not produce very good results.

To make the python script work pythainlp must be installed using the
pip (pip3) package manager.

### 2. Load the required extensions into your database
```sql
CREATE EXTENSION osml10n CASCADE;
CREATE EXTENSION osml10n_thai_transcript CASCADE;
```


Afterwards you should be able to do the following:

```sql
yourdb=# select osml10n_translit('北京');
 osml10n_translit
---------------
 běi jīng
 (1 row)
```

```sql
yourdb=# select osml10n_kanji_transcript('漢字');
 osml10n_kanji_transcript
---------------------
 kanji
 (1 row)
```

```sql
yourdb=# select osml10n_thai_transcript('ถนนข้าวสาร');
 osml10n_thai_transcript
---------------------
 thnn khaotan
 (1 row)
```

To check if everything went well run the test script provided in the
tests/runtests_in_virtualenv.sh directory. As this test uses pg_virtualenv
it is not required to create a database to run the test.


