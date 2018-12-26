# Extracting Browser History

To extract browser history, you usually have to use the sqlite database where it is stored.

## Firefox and forks (e.g. Waterfox)

You can use the following SQL commands ([source](https://support.mozilla.org/de/questions/835204)) to get the history from the places.sqlite file from your profile folder (in `.mozilla` on Linux).

    SELECT datetime(moz_historyvisits.visit_date/1000000,'unixepoch'), moz_places.url, moz_places.title
    FROM moz_places, moz_historyvisits
    WHERE moz_places.id = moz_historyvisits.place_id

I used the sqlite-manager addon (you can save the results as CSV using the Actions button), it should also work with your local SQL program.

## Chromium / Chrome

For Chromium I used these SQL commands ([source](https://superuser.com/questions/602252/can-chrome-browser-history-be-exported-to-an-html-file)) on the `History` file (in your profile folder, in `.config` on Linux).

    $ sqlite3 History
    sqlite> .headers on
    sqlite> .mode csv
    sqlite> .output my-history.csv
    sqlite> SELECT datetime(last_visit_time/1000000-19644473600,'unixepoch','localtime'), url FROM urls ORDER BY last_visit_time DESC

The output will be saved in `my-history.csv`.

# Parse CSV

Pasring the CSV is sometimes not trivial. In the case of Firefox, the CSV contains the page title wich can include funny symbols and unclosed quotes.

I wrote a script `extract_fox.py` that works in most cases (only skips lines containing `","` (including the quotes) and the last line). The last part of the script prints the history.

The chromium script is very easy since it only contains time/url, I added it to the repo, too.

You can redistribute the python code under the MIT/X11 license, the stackexchange and mozilla support snippets are licensed under creative commons (CC BY-SA, see respective websites for more info).
