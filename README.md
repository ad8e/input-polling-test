# Overview
The idea behind test.exe is to output a number whenever you press a key or move your mouse over the window. Each number represents the millisecond time difference between the latest input and the previous input.

This was originally created to test issues with Thinkpad keyboards, but it can test any mouse or keyboard. https://www.reddit.com/r/thinkpad/comments/9j70v6/thinkpad_keyboard_firmware_worse_than_anyone/

# How to use it
First, in some text editor, press asdf simultaneously. If the letters always come out in a specific order (for example, always fdsafdsafdsa instead of scrambled like asfdfadsdsaf), then your keyboard has a ordering problem. If you can press them not exactly at the same time and the ordering still persists, then the ordering problem is bigger. The size of the ordering problem represents how much you must slow down your typing in order to not scramble keys.

If you have an ordering problem, run test.exe to get more precise data. When you press a key or move your mouse, you'll see some numbers and letters. Each line gives the key that was pressed, and the time in milliseconds since the previous press. Try pressing specific chords (two-button keypresses) like kl and sl simultaneously, and also closely-but-not-simultaneously. Large numbers like 1000 represent the time to the previous chord, and small numbers like 24 represent the time between the two keys of the chord. Ignore the large numbers.

The ideal result is that every integer from 0 to 20 can appear in the output. This represents a 1000Hz scanning keyboard with no sampling problems. You'll see this only on high end keyboards.

A standard result, on most OEM keyboards, is that you can achieve only numbers 0, 8, 16, 24, 32, ... This represents a 125Hz keyboard.

A poor result, on high end keyboards with bad firmware, is that you achieve every number from 0 to 50 but still have an ordering problem when testing in a text editor. This represents 1000Hz reporting, but bad scanning behavior internally. It means you bought an expensive keyboard that is not any good.

Lenovo Thinkpad X/T keyboards have a different result, where specific chords have outcomes 8ms apart, different chords have different outcomes in 0.5ms increments, and the lowest non-0 outcome is 15.5ms. This represents a 2000Hz-report-rate keyboard with a 125Hz scan rate and a 23ms ordering problem.

# How to build it
Windows Visual Studio:

If GLFW comes with a pre-compiled library for your Visual Studio version, you may be able to use that. Otherwise, you must build GLFW from source. Here were my steps for VS2017:

After opening the solution in Visual Studio, select the GLFW project, and change in Project Settings: C/C++ -> Code Generation -> Runtime Library -> remove the "DLL" part

Then build GLFW. Then in my project, use these settings:

Project Settings: C/C++ -> Language -> C++ Language standard = std:latest. Add the glfw library path to VC++ Directories -> Library Directories (glfw-folder/src/debug). Add glfw3.lib to Linker->Input->Additional Dependencies. Add the GLFW include folder to VC++ Directories -> Include Directories. In C/C++ -> Code Generation -> Runtime Library -> remove the "DLL" part

Then build. My exact settings, for a reproducible build, were VS2017, Multi-threaded (non-DLL), Release mode, x64, GLFW 3.2.1.

Mingw-w64:

    pacman -S mingw-w64-x86_64-glfw
    g++ Source.cpp -std=c++17 -lglfw3 -O3 -static -I"glfw-folder-here/include" -lole32 -lgdi32 -lwinmm -s

Linux:

    sudo apt install libglfw3-dev
    g++ Source.cpp -std=c++17 -lglfw -Ofast
    ./a.out
