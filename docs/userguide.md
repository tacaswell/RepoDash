<h1>RepoDash User Guide</h1>

<h3>Pre-requisites</h3>

RepoDash was written with and tested against python 3.7.3 on a linux PC (Raspbian 10). It hasn't currently 
been tested on Windows or against any earlier versions of python. However, the codebase makes use of 
f-strings, a feature that was introduced in python 3.6. 

The codebase makes use of the following python modules, some of which are included in the core python 3.7.3 
installation, and some of which may require installation:

- os
- sys
- argparse
- re
- requests
- pandas
- numpy
- calendar
- sqlalchemy
- dateutil
- matplotlib
- mpl_toolkits
- mplcursors
- subprocess, platform, struct, locale (for bug reporting)
- pytest, pytest-flake8, pytest-mypy (for unit testing)

Future plans include an install script that ensures the supported version of python and all required modules are 
present in your python installation. For now though, you'll need to ensure the above pre-requisites have been met.

If you encounter an error message of the following form when you run RepoDash.py you are missing a python module 
that needs installing manually

    ModuleNotFoundError: No module named 'requests'

To install a missing python3 module open a terminal window, type the following on the command line and follow any 
on-screen instructions:

    python3 -mpip install MODULE_NAME

<h3>Installing and Running RepoDash in Demo Mode</h3>

To download RepoDash from Github and run it in default (demo) mode, open a terminal window and 
type the following commands 

    cd YOUR-CHOICE-OF-FOLDER
    git clone https://github.com/LaurenceMolloy/RepoDash.git
    cd RepoDash/src
    python3 RepoDash.py

This processes the first 10 pages of the matplotlib repository issues list, 1000 issues in all, 
and plots metrics for the most recent 12 months of issues processed.

<h3>Running RepoDash With Command Line Arguments</h3>

By using command line arguments, you can run RepoDash against any public Github repository, request 
any number of pages starting from any specific page of the issues list, and output issues list metrics
for any time period of your choosing. You can also choose whether to look at pull request metrics or 
issue metrics. The available command line options are as follows:

<p>
<table>
<tr><th>-a, --authtoken</th>     <th>Github Personal Access Token</th>            <th>(default: '')</th></tr>
<tr><th>-u, --user</th>          <th>Github username or account</th>              <th>(default: 'matplotlib')</th></tr>
<tr><th>-r, --repo</th>          <th>Github repository name</th>                  <th>(default: 'matplotlib')</th></tr>
<tr><th>-t, --type</th>          <th>Issue type ('issue', 'pull_request')</th>    <th>(default: 'issue')</th></tr>
<tr><th>-m, --months</th>        <th>plot metric analysis timespan in months</th> <th>(default: 12)</th></tr>
<tr><th>-d, --refdate</th>       <th>plot metric reference end date</th>          <th>(default: now)</th></tr>
<tr><th>-f, --firstpage</th>     <th>first page number to request</th>            <th>(default: 1)</th></tr>
<tr><th>-c, --pagecount</th>     <th>number of pages of issues to request</th>    <th>(default: 10)</th></tr>
<tr><th>-o, --offsetmonth</th>   <th>which month to offset issue closure in ('opened', 'closed')</th>  <th>(default: closed)</th></tr>
<tr><th>-s, --savefile</th>      <th>save dashboard image to a file</th>          <th>(default: ./metrics.png)</th></tr>
<tr><th>-il, --inlabfile</th>    <th>labels/groups mapping file (CSV)</th>        <th>(default: ./label.csv)</th></tr>
<tr><th>-ol, --outlabfile</th>   <th>file to write labels/groups out to (CSV)</th><th>(default: ./outlabel.csv)</th></tr>
<tr><th>-nl, --numlabs</th>      <th>number of labels to plot in the top N label count display</th><th>(default: 12)</th></tr>
<tr><th>-p, --datapath</th>      <th>location of SQLite database</th>             <th>(default: REPODASH_PATH/data)</th></tr>
<tr><th>-i, --info</th>          <th>write environment info for debugging</th>    <th>writes a debug_info.txt file</th></tr>
<tr><th>-h, --help</th>          <th>print options/help text</th>                 <th></th></tr>
</table>
</p>

<h3>Notes</h3>

**Database Interaction:** The current version of RepoDash wipes and re-generates the database with every run. 
An update mode of operation is planned in the future. This will allow you to focus only on changes and additions since 
the previous run, reducing the number of API calls required.

**Output:** By default, RepoDash renders the output as an image in a window on the desktop. You can override this and 
save the image to a file for sharing by email or inclusion in a report by using the **-s** argument. When used without 
specifying a filename, it defaults to saving to a file called _**metrics.png**_ in the current folder. There is no 
interactive functionality when saving the image to a file.

**Grouping Issue Labels:** By default, RepoDash will display the frequency of the top 12 labels used for all issues that 
remained open at the end date of the time period of interest. To do this, it first has to read in the full list of issue
labels via the Github API. You can save this list to a CSV file using the **-ol** (output label file) argument. It is 
sometimes desirable to be able to organise the project's issue labels into groups (for instance, grouping all labels that 
refer to an aspect of a project's API). This can be done by manually editing the saved CSV file to associate multiple 
'label' values (first column) with a common label_group value (second column) and then supplying this edited file to the 
program using the **-il** (input label file) argument.

**Authentication:** You can run RepoDash against any public Github repository without authentication. 
This limits you to 60 Github API web requests per hour. If you have a Github account you can request a 
_Personal Access Token_ from <A HREF="https://github.com/settings/tokens">HERE</A> (selecting the _Repo_ 
scope) and supply it on the command line using the **-a** option. This will increase your requests limit 
to 5000 per hour.

**Out Of Range Timespans:** If you specify a reference date for metrics and/or request a timespan that falls 
outside the date range of the issues data collected, RepoDash will do its best to adjust the dates and/or 
shorten the plotting timespan so that it maps as closely as possible to the data available.

<h3>Example Usage</h3>

**EXAMPLE 1: Numpy Issues** 

Process the first 6 pages of the issues list from the Numpy repository and plot _**issue**_ and _**label**_ metrics for the 
period June 2012 to September 2012 inclusive (4 months). You may notice that there are labels with a count of zero in the 
label count display. These are labels that were attached to issues that were both opened and closed during the period June 
2012 - September 2012.

    python3 RepoDash.py -u numpy -r numpy -m 4 -d '2012-09' -c 6

![Screenshot](images/RepoDash_UserGuide_Ex1_Numpy_issue.png)


**EXAMPLE 2: Numpy PRs** 

Process the first 6 pages of the issues list from the Numpy repository and plot _**pull-request**_ and _**label**_ metrics 
for the period June 2012 to September 2012 inclusive (4 months).

    python3 RepoDash.py -u numpy -r numpy -m 4 -d '2012-09' -c 6 -t pr

![Screenshot](images/RepoDash_UserGuide_Ex2_Numpy_pr.png)


**EXAMPLE3: Pandas Issues (with issue closure offset in the month in which it was closed)** 

Process pages 200 to 249 (50 pages) of the issues list from the Pandas repository and plot the last 6 months of issue 
metrics and the top 20 labels used.

    python3 RepoDash.py -u pandas-dev -r pandas -m 6 -f 200 -c 50 -nl 20

![Screenshot](images/RepoDash_UserGuide_Ex3_Pandas_issue.png)

**Notes**
- The silent **-o** (offset month) option defaults to 'closed', which calculates _Total Open Issues_ as a simple 
aggregate of issues opened - issues closed in each month.
- The silent **-d** (reference date) argument defaults to _now_. This falls outside of the date range observed in the data 
processed, so RepoDash maps the plotting timeframe to the last 6 calendar months of the data (6 months is our requested timespan).
- RepoDash has no knowledge of any issues that were opened or closed prior to the earliest issues processed. For simplicity, it
presumes an empty issues list prior to this point. The Total Open Issues count is therefore relative to a zero count at the start
of the processed data timespan (February 2018 in this example).

**EXAMPLE4: Matplotlib Issues with _grouped_ label counts**

Process pages 100 to 139 (40 pages) of the issues list from the Matplotlib repository and plot the last 12 months of issue 
metrics and label counts, grouped according to a label grouping file (supplied using the **-il** argument).

    python3 RepoDash.py -u matplotlib -r matplotlib -m 12 -f 100 -c 40 -il ../config/example_matplotlib_label_file.csv

![Screenshot](images/RepoDash_UserGuide_Ex4_Matplotlib_issue_grouped_labels.png)
