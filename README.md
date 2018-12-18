# _Pasthis_

## Privacy, security, simplicity

_Pasthis_ is a pastebin written in [PHP](https://www.php.net) using
[SQLite](https://sqlite.org/) as database backend and
[prettify](https://code.google.com/p/google-code-prettify/) to handle the syntax
highlighting.

![Pasthis screenshot](resources/screenshot.jpg)

_Pasthis_ was designed with privacy, security and simplicity in mind:

- Expired pastes are automatically deleted.
- Paste IDs aren't predictable (their URL cannot be guessed easily).
- No statistics, counter or public list of pastes is available.
- The use of HTTPS to post or get the pastes is enforced.
- A trivial anti-spam filter is built-in.
- By default the content is not formatted nor changed in any way (no wrap, no
  syntax highlighting). It is a user driven choice to do so.
- Pastes can be displayed as raw.
- A command-line tool can be downloaded to post pastes directly from terminals.
- Tabulations are handled within the textarea to avoid changing the focus.
- The design is mobile friendly (responsive) :blush:

## Deployment

1. Download _[Pasthis](https://github.com/moulecorp/pasthis)_.
2. Configure the web server:
   - Apache: edit the RewriteBase directive in the
     [.htaccess](https://github.com/moulecorp/pasthis/blob/master/resources/.htaccess)
     if needed.
   - Nginx: see the provided
     [nginx.conf](https://github.com/moulecorp/pasthis/blob/master/resources/nginx.conf).
   - Caddy: see the provided
     [Caddyfile](https://github.com/moulecorp/pasthis/blob/master/resources/Caddyfile).
3. Make sure that the folder is _readable_ and _writable_ by www-data, since
   this is required by PHP to be able to create the SQLite database.

It is required to call the cron method on a regular basis to delete expired
pastes, as a privacy concern. To do this on a GNU/Linux machine edit the
`/etc/crontab` file and add the following line:

```
*/10 * * * * www-data php /path/to/pasthis/index.php
```

Be aware expired pastes can only be deleted when requested or when the cron
method is called. Without the previous cron configuration, their deletion can't
be ensured. They just won't be displayed.

## Update

1. Update to the latest version (keep the database!).
2. Run `php update.php`.

## Command line tool

A [command line tool](https://github.com/moulecorp/pasthis/blob/master/pasthis.py)
is available allowing you to post files. In order to take advantage of this
utility, download it, make it executable and display the help output for more
information:

```
chmod +x ./pasthis.py
./pasthis.py --help
./pasthis.py --url https://www.example.net/pasthis/ file.txt
```

## Anti-spam

Every time a paste is sent, a value (called degree) is associated to
the poster's ip hash. It is used in the following formula:

```
T = time() + intval(pow(degree, 2.5))
```

If the user posts another paste after T, the degree is reset to zero.
If he tries before T, the degree is incremented, and the paste is denied.

There is also an hidden field, that set the degree to 512 (which corresponds
to ~72h) if filled.

## Authors and License

```
Copyright (C) 2014 - 2018 Julien (jvoisin) Voisin - dustri.org
Copyright (C) 2014 - 2018 Antoine Tenart <antoine.tenart@ack.tf>

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA
02110-1301, USA.
```
