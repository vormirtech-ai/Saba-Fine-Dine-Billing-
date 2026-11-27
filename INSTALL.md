# Installing Saba in the restaurant

This is the whole product. There is nothing to compile and nothing to
download: no `npm install`, no build step, no framework, no dependencies.

> **The website is not the till.** https://vormirtech-ai.github.io/Saba-Fine-Dine-Billing-/
> is a demonstration that runs inside a browser tab, because GitHub Pages
> serves static files and a till is a server with a database. What goes in
> the restaurant is this repository, on one computer in the back office.
> The two share every line of their pricing and ordering code, which is why
> the demonstration shows the real thing.

```
WHAT YOU NEED
================================================================

  Node.js 22.5 or newer, on one computer that stays switched on.

  Check what you have:        node --version

  If it is older than 22.5, get it from https://nodejs.org (the "LTS"
  download is fine as long as the number is 22.5 or higher).

That computer is the till. Everything else in the building — the captain's
phone, the tablet at the pass, the laptop in the office — is a browser
pointed at it. It does not need the internet. It needs the restaurant's
own wi-fi, and nothing else.


================================================================
RUNNING IT — THE SHORT VERSION
================================================================

  1. Put it on that computer, somewhere permanent — not the desktop and
     not Downloads. A folder like  C:\Saba  or  /opt/saba  is right.

         git clone https://github.com/vormirtech-ai/Saba-Fine-Dine-Billing-.git saba
         cd saba

     No internet on the till? Download the ZIP from the repository's green
     Code button on any computer, carry it over, and unzip it there.

  2. Open a terminal in that folder.

         Windows:  open the folder, type  cmd  in the address bar, Enter
         macOS:    right-click the folder, Services, New Terminal at Folder
         Linux:    you know

  3. Start it:

         npm start

     It prints something like this, and the two addresses at the bottom
     are the ones you want. Write the second one down.

         FIRST RUN - the owner (admin) PIN for this install is:  5776
         Sign in with it, then add your staff under Setup, People.
         It is shown once.

         Saba Restaurant Suite 2.0.0 is running  port=8080
           open http://localhost:8080
           open http://192.168.1.40:8080

     (There is also a warning that SQLite is an experimental feature.
      That is Node talking about itself. Ignore it.)

  4. On the same computer, open the first address:  http://localhost:8080

     There is one account on a new till, called Owner, and that PIN is
     its PIN. Sign in with it. It can reach everything.

     There is no fixed, out-of-the-box PIN, on purpose: a till that
     ships with 1234 on every install has no access control at all.
     Yours is generated for your install and shown once.

     Missed it, or want to choose it yourself? Either works:

         npm run owner-pin -- 4812      sets it, any time, at the till

         set SABA_SETUP_PIN=4812        choose it before the FIRST run
         npm start                      (Windows; on Linux put it in
                                         front: SABA_SETUP_PIN=4812 npm start)

     Change it straight away — Setup, People, Owner — and add the rest of
     your staff there, each with their own four digits. Nobody shares a
     PIN: the audit trail is worthless if two people are one person.

  5. On every other screen in the building — the captain's phone, the
     tablet at the pass, the laptop in the office — open the SECOND
     address, the one with numbers in it:

         http://192.168.1.40:8080

     and sign in with that person's own PIN. That is the whole install.

     (Yours will be a different number. It is whatever the till printed.)

     If a phone cannot reach it, it is on a different wi-fi, or the
     till's firewall is blocking the port. Both are covered in
     docs/DEPLOYMENT.md.


================================================================
WHAT TO DO ON THE FIRST EVENING
================================================================

  Setup  (the owner only)

    - The bill header: name, address, phone, GSTIN, FSSAI. This is printed
      on every bill, so get it right once.
    - Service charge and tax rates.
    - The rooms and the tables in them.
    - Everybody's account and their own PIN. Nobody shares a PIN; the
      audit trail is worthless if two people are the same person.
    - The kitchen printer, if you have one — see below.

  Menu

    - The carte ships with a full fine-dining menu as an example. Change
      the prices, retire what you do not serve, add what you do.
    - A dish can have a half price as well as a full one. Set it and the
      order screen offers both.

  Then trade. Nothing else is needed.


================================================================
THE KITCHEN PRINTER
================================================================

Setup, Printing, Kitchen printer.

  A network printer:   192.168.1.50:9100
                       (9100 is the port nearly all of them listen on;
                        the printer's own menu will tell you its address)

  A USB printer on Linux:   /dev/usb/lp0

  On Windows, use the network address. A USB thermal printer plugged
  into a Windows till is reached by sharing it and giving the share's
  path (\\TILL\Kitchen), but a printer with an Ethernet socket and a
  fixed address is far less trouble and survives the till being
  restarted.

There is a Test page button beside it. Once it is set, a docket prints by
itself the moment a captain fires a course — no dialogue, nobody pressing
anything.

If the printer is off, out of paper or unplugged, nothing stops. The
kitchen screen has the docket, and the till writes one line in its log.


================================================================
WHO CAN DO WHAT
================================================================

  Admin (the owner)   everything, including the takings, Setup, staff,
                      backups and the cloud
  Manager             runs the service — floor, orders, kitchen, bills,
                      bookings, the menu, the roster. Never the takings.
  Captain             floor, orders, kitchen, bills, bookings, own hours
  Waiter              floor, orders, own hours
  Kitchen             the kitchen display, and nothing else

The server enforces this, not the screen. A waiter who types the reports
address into the browser gets a refusal, not a report.


================================================================
ON A PHONE
================================================================

The same application. No separate app to install, nothing from a store.
Open the till's address in the phone's browser and add it to the home
screen if you like.

On a phone the sections move to a bar across the bottom, under the thumb,
and the order screen becomes two tabs — the carte, and this table — so
each gets the whole screen. A tablet in portrait keeps both side by side.


================================================================
IF THE INTERNET GOES DOWN
================================================================

Nothing happens. The till is in the building; the internet is not part of
it. Tables are seated, dockets fire, bills print and the day closes
exactly as usual.

If you have connected the optional cloud copy, it catches up by itself
when the line comes back, without anybody doing anything.


================================================================
BACKUPS
================================================================

Setup, Backups, Take a backup now. It writes a complete, self-contained
copy. Take one at close every night and keep it somewhere that is not
that computer.

To restore one:   npm run restore -- <the backup file>


================================================================
RUNNING IT PROPERLY, SO IT SURVIVES A RESTART
================================================================

The short version above stops when you close the terminal. For a real
install, so it comes back by itself after a power cut:

  Linux:     sudo ./install.sh     (sets up a systemd service)
  Docker:    docker compose up -d
  Windows:   run it as a service with NSSM — docs/DEPLOYMENT.md

docs/DEPLOYMENT.md has all three in full, plus the printer, the network,
and what to do when something goes wrong.


================================================================
THE REST OF THE PAPERWORK
================================================================

  README.md               what it is and what it does
  docs/DEPLOYMENT.md      installing it properly
  docs/OPERATIONS.md      running it, for the restaurant
  docs/HANDOVER.md        how it is built, for a developer

Checking it works:   npm test
                     111 tests, a few seconds, nothing mocked.
```
