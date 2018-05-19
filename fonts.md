# Fonts

## 6x13 "fixed"

rpm is ucs-miscfixed-fonts-0.3-11.el7.noarch

- http://www.chiark.greenend.org.uk/~sgtatham/fonts/
- Download `fixed.fon` (under heading "**fixed for Windows**").
- Once installed, the font is available as "fixed".

Without admin rights, try one of the following:

- Download `registerfont.exe` & call it from a cmd script in startup folder.
- Call `AddFontResourceA` as follows:
  - `rundll32.exe gdi32.dll,AddFontResourceA fixed.fon`
  - `rundll32.exe user32.dll,SendMessage 65535 29 0 0`
- Double click the font file to view it.
  - While the viewer is open the font may be available
    to other applications (works on XP).
