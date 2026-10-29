game_splitterNX
Split, join, check and fix Nintendo Switch game files (.nsp, .xci, .nsz, .xcz) for FAT32 SD cards. Runs on Android (Termux), Windows, Linux and macOS. No dependencies: just Python 3.
by webcrawler · MIT License
  ╭───────────────────────────────────────╮
  │ ⣿⣿⣷⣶⣦⣤⣄⣀⡀  GAME SPLITTER NX           │
  │ ⠛⠛⠿⠿⠶⠶⠤⠤⠄  split • join • check • fix │
  │            by webcrawler  v2.0.0      │
  ╰───────────────────────────────────────╯
Features
Split into 4 GB (just under the FAT32 limit), 3 GB or 2 GB parts
Free-space check before starting, and a dotted progress bar with speed and ETA
Quick verification after every split
Corruption check for .nsp and .xci files and their split folders
Full compare of a split folder against the original, byte for byte
Fix the "shows as a folder" problem by setting the archive bit (Windows, Linux, macOS)
Join a split folder back into one file, and clean up old split folders
Split several games in one go; remembers your last part size
Safe Ctrl+C: partial files are removed
Supported formats
Format
What it is
Split / join
Compare with original
Corruption check
.nsp
Digital game package
Yes
Yes
Full: every data file is re-hashed
.xci
Game card image
Yes
Yes
Full: header, partition and file hashes, plus every data file
.nsz
Compressed .nsp
Yes
Yes
Structure only
.xcz
Compressed .xci
Yes
Yes
Structure only
Splitting just cuts a file into pieces, so it works on any file type. The format is detected from the file's contents, not its extension.
Compressed files. Their data files (.ncz) can only be hash-checked after decompressing, which needs libraries this tool deliberately doesn't use. For a full check of .nsz/.xcz, use the official nsz tool:
pip install nsz
nsz --verify game.nsz
Install
Get the code in one of two ways.
Option A: clone with git (easiest to keep up to date)
git clone https://github.com/Popflare/game_splitterNX.git
cd game_splitterNX
To update later, run git pull inside the folder. On Termux, install git first with pkg install git.
Option B: no git. On the repository page, tap Code → Download ZIP and unzip it, or just open game_splitterNX.py, tap Raw, and save the file.
Then follow the setup for your system.
Android (Termux)
Install Termux from F-Droid (the Play Store version is outdated).
In Termux, run these once:
pkg update
pkg install python git
termux-setup-storage
Tap Allow on the storage permission prompt.
Get the script into Termux, either way:
Cloned: the script is at ~/game_splitterNX/game_splitterNX.py.
Downloaded: save game_splitterNX.py to your phone's Download folder. It is then at ~/storage/downloads/game_splitterNX.py.
The menu always looks for your games in the phone's Download folder, wherever the script itself lives.
Windows / macOS / Linux
Install Python 3, then run the script directly from the folder you cloned or unzipped.
Usage
Menu (Termux, or any terminal without a GUI)
python ~/game_splitterNX/game_splitterNX.py
(If you downloaded the single file into your Download folder instead of cloning, use python ~/storage/downloads/game_splitterNX.py.)
Key
Action
1
Split one or more games (type 1, 1,3 or all), then choose a part size
2
Join a split folder back into one file
3
Clean up old split folders
4
Check a game for corruption
5
Compare a split folder with its original
6
Fix "shows as a folder" (set the archive bit)
h
Help
q
Quit (a blank Enter just redraws the menu)
Split parts are written to Download/split/<game name>/.
Desktop window
On a computer with Tkinter, running the script with no arguments opens a small window with file pickers and a progress bar.
Command line
python game_splitterNX.py game.nsp                 # 4 GB parts, output to ./split/
python game_splitterNX.py game.xci -s 2            # 2 GB parts
python game_splitterNX.py game.nsp -o /some/folder # custom output folder
python game_splitterNX.py game.nsp --overwrite     # replace an existing split folder
python game_splitterNX.py game.nsp --check         # check for corruption
python game_splitterNX.py split/game.nsp --check   # check a split folder
python game_splitterNX.py --fix-archive /path/to/sdcard
Option
Meaning
-o, --output
Output directory (default: a split folder next to the file)
-s, --size
Part size in GiB: 2, 3 or 4 (default: just under 4)
--overwrite
Replace an existing split folder
--check
Check a game file or split folder for corruption instead of splitting
--fix-archive PATH
Set the archive bit on split game folders under PATH (Windows, Linux, macOS)
--ascii
Plain characters instead of dots and box art
--version
Show the version
One-tap launch on Android (optional)
Install Termux:Widget from F-Droid.
In Termux, run:
mkdir -p ~/.shortcuts
echo 'python ~/game_splitterNX/game_splitterNX.py' > ~/.shortcuts/Split-Game
chmod +x ~/.shortcuts/Split-Game
Add the Termux:Widget widget to your home screen and tap Split-Game.
(If you saved the file to your Download folder instead of cloning, use ~/storage/downloads/game_splitterNX.py in the first command.)
After splitting: getting it onto the Switch
Copy the whole folder (game.nsp/ with 00, 01, ... inside) to your SD card. Don't copy the parts loose.
Set the archive bit on the card (see the next section). Do this after copying.
Open your installer and install from the SD card. The folder should appear as a single file of the full size.
Installers differ in how they handle split files, so check the documentation for the one you use.
The Switch shows a folder, not a game
Why it happens. A split game is a folder (game.nsp/) containing parts 00, 01, 02... The Switch only treats that folder as one file when the folder has the FAT archive bit set. Without it, the console and installers see an ordinary folder and refuse to install it.
Set the bit after copying to the SD card. Copying a folder onto a FAT32 card can clear the bit, so setting it on the original folder isn't enough. Set it on the card itself, using any of these:
On the Switch (works from Android, no computer needed)
Hekate: boot Hekate, then go to Nyx → Tools → Arch bit • RCM • Touch • Pkg1/2 → Fix Archive Bit. This fixes every folder on the card at once.
NX-Shell: open the folder's parent, highlight the folder, press X, and look for Set archive bit (menu names can differ between versions). This fixes one folder.
On a computer, automatically from this script
Plug the SD card into the computer, copy the game folders onto it, then run menu option 6 or --fix-archive:
System
How the script does it
Notes
Windows
attrib +A
Works on the card's drive letter, e.g. --fix-archive E:\
Linux
Sets the FAT attribute directly (same as the fatattr tool)
The card must be mounted as FAT32 (vfat) and the folder must be yours to modify
macOS
sudo chflags arch
Asks for your password; FAT32 cards only
Android
Not possible
Termux can't change FAT attributes on the card without root. Use Hekate or NX-Shell on the Switch
If the script can't set the bit, it prints the manual command to run instead. The Linux and macOS helpers are newer and less tested than the rest, so please open an issue if one doesn't work on your system.
exFAT cards don't need any of this. exFAT has no 4 GiB file limit, so just copy the original file without splitting.
Checking for corruption
Quick verification (automatic after every split). Checks the part count, the exact size of each part, and the first and last 64 KiB of every part against the original. It reliably catches truncated or incomplete copies, but it can miss damage in the middle of a part.
Corruption check (menu option 4, or --check). Works on an original file or a split folder and reads the whole game, so expect a minute or two.
.nsp: each .nca inside is named after the first half of its own SHA-256 hash. The tool re-hashes every .nca, compares it to its name, and checks that nothing is cut off.
.xci: also verifies the header hash, each partition's hash, and the stored hash of every file's header, then re-hashes every .nca against its name.
.nsz / .xcz: structure check only; see Supported formats.
A cut-off file or a hash mismatch on a split folder whose original passes means real damage: copy or split again. A hash mismatch can occasionally be a false alarm if the file was converted or repacked by another tool, because those tools can change the data without changing the name.
Full compare (menu option 5). Compares a split folder against the original file byte for byte. Use it after splitting .nsz/.xcz files, or any time you want certainty that the parts match. It needs the original to still exist.
Troubleshooting
The installer shows an empty list or a plain folder. The archive bit isn't set. See The Switch shows a folder, not a game.
"Not enough free space." The split is a second copy of the game. Free up space equal to the game's size, or delete the original after a verified split.
"Output already exists." A split folder with that name is already there. Use the menu's overwrite option, or --overwrite.
"File is already small enough." The file is under the part size, so it doesn't need splitting.
Dots or boxes look broken. Run with --ascii for plain characters.
Disclaimer
This tool only splits, joins, checks and fixes file attributes; it does not decrypt, modify, or download any game content. Use it only with files you legally own, such as dumps of your own games. Don't ask for or share copyrighted game files in this repository's issues.
This project is not affiliated with or endorsed by Nintendo.
License
MIT © 2026 webcrawler
