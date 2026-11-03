# McGill Master's Thesis

It has been over a decade since I last read my Master's thesis, and I had completely forgotten most of what was in it. Now that I have over a decade of work experience, I got a little curious as to what I had written back then and I decided to re-read my own Master's thesis from 2013 in November 2026.

I did find some typos that I should probably mention and correct. But overall I am actually impressed with my own Master's thesis from 2013. I didn't think it would be good since I did not have much work experience back then when I wrote my thesis and I questioned how much would past me would even know about these topics, but I was pleasantly surprised!

My Master's thesis still holds solid after over a decade of work experience as a Hardware Verification Engineer who frequently worked with assertions, and while reading my own thesis again, I even re-learned quite a few things from my logic synthesis courses that I had forgotten after working in the field for so long.

If you want to read my Master's thesis, you can find it on:
```
https://mcgill.scholaris.ca/home
```
Search for "Danny Sarraf" and my thesis should show up where you can read it for free:
```
https://mcgill.scholaris.ca/items/85543aa4-f23f-4861-9b28-83b7096a41cb
```

The title of my Master's thesis was:
"Optimizing assertions in semi-formal assertion-based verification"

I hope you like it and find it informative if you do choose to read it!

# Corrections to my Master's thesis

Below are some corrections to my Master's thesis from 2013 that I mentioned. The corrections do alter some of the Karnaugh maps and therefore the set of assertions that fully cover the Karnaugh maps. That being said, the lessons learned and conclusions from the exercises remain fully valid and unchanged.

Correction 1: If the FIFO is full and you do a simultaneous read and write, the FIFO should still be full. Similarly for empty.
Correction 2: If the FIFO is full and you do a simultaneous read and write, an overflow should not occur. Similarly for empty and underflow.
Correction 3: A FIFO can never be full and empty at the same time and should therefor be marked as x in the Karnaugh maps.

NOTE:
```
In all of the Karnaugh maps
F  refers to when the FIFO is full     (both input and output)
E  to when the FIFO is empty           (both input and output)
O  to overflow                         (output only)
U  to underflow                        (output only)

R  to a read attempt                   (input only)
W  to write attempt                    (input only)
A  write address =  read address - 1   (input only)
B  read address  = write address - 1   (input only)
```

The corrections mentioned above results in the following Karnaugh maps updates:

## 6.1 Minimizing Assertions by Optimizing the Boolean Function

### Karnaugh map for full:

```
         RF
        │00 01 11 10
      ──┼────────────
   WA 00│ 0⎞⎛1⎞⎛0 ⎛0⎞⎞
      01│ 0⎠⎜1⎟⎝0 ⎜0⎟⎠
      11│(1 ⎜1⎟ 1⎞⎜0⎟
      10│ 0)⎝1⎠ 1⎠⎝0⎠
          F

   F = R'F + WAR' + WF
   F'= RW' + RF' + W'F' + A'F'
```


### Karnaugh map for empty:

```
         WE
        │00 01 11 10
      ──┼────────────
   RB 00│ 0⎞⎛1⎞⎛0 ⎛0⎞⎞
      01│ 0⎠⎜1⎟⎝0 ⎜0⎟⎠
      11│(1 ⎜1⎟ 1⎞⎜0⎟
      10│ 0)⎝1⎠ 1⎠⎝0⎠
          E

   E = W'E + RBW' + RE
   E'= WR' + WE' + R'E' + B'E'
```


### Karnaugh map for overflow:

```
       RF
      │00 01 11 10
     ─┼────────────
   W 0│(0⎞ 0 ⎛0 ⎛0⎞)
     1│ 0⎠(1)⎝0 ⎝0⎠
        O

   O = WR'F
   O'= W' + R + F'
```

### Karnaugh map for underflow:

```
       WE
      │00 01 11 10
     ─┼────────────
   R 0│(0⎞ 0 ⎛0 ⎛0⎞)
     1│ 0⎠(1)⎝0 ⎝0⎠
        U

   U = RW'E
   U'= R' + W + E'
```

Based on the Karnaugh maps, full and empty require 7 cubes/assertions each, overflow and underflow require 4 cubes/assertions each, for a total of 22 assertions/cubes.

### Merged Karnaugh Map for {F,O}:

```
          RF
        │ 00  01  11  10
      ──┼────────────────
   WA 00│ 00⎞⎛10⎞⎛00 ⎛00⎞⎞
      01│ 00⎠⎝10⎠⎝00 ⎜00⎟⎠
      11│(10)⎛11⎞⎛10⎞⎜00⎟
      10│ 00)⎝11⎠⎝10⎠⎝00⎠
          FO

   F'O'= W'F' + W'R + RF' + A'F'
   F'O = 0 (never!) (no assertion needed as truth table fully covered...)
   FO' = W'R'F + WRF + WAR'F'
   FO  = WR'F
```

The merged Karnaugh Map for {F,O} requires a total of 8 assertions/cubes in total instead of 11

### Merged Karnaugh Map for {F,E}:

```
            RBE
          │ 000 001 011 010 110 111 101 100
       ───┼─────────────────────────────────
   WAF 000│ 1  ⎠⎛ e 8 e⎞⎝   ⎛9 e⎛ e⎞0 e⎞⎛2 ⎞
       001│  f ⎞⎜xx  xx⎟⎛f  ⎜  e⎜ e⎟  e⎟⎜  ⎟
       011│5 f ⎠⎜xx  xx⎟⎝f  ⎜  e⎜ e⎟  e⎟⎜  ⎟
       010│    ⎞⎝ e   e⎠⎛   ⎝  e⎜ e⎠  e⎟⎝  ⎠
       110│ ⎛f   f   f 6 f ⎞   )⎜ e   e⎟(
       111│ ⎛f   f   f   f   f  ⎜xx  xx⎟ f ⎞
       101│ ⎝f   f   f 7 f   f  ⎜xx  xx⎟ f ⎠
       100│ (3             )  4)⎝ e   e⎠(
             FE
```

NOTE: The Karnaugh map above contains numbers 0-9 and A-C that count the different cubes and help to more easily track which cube applies to the functions below.
NOTE: xx in the table above refers to the fact that full and empty can never occur at the same time.

```
      F'E' = W'F'R'E' + RW'B'E' + WR'A'F' + WE'RF' (1+2+3+4)
      F E' = FR' + WAR' + WF                       (5+6+7)
      F'E  = W'E + W'RB + RE                       (8+9+0)
      F E  = 0 (never reachable)
```

The merged Karnaugh Map for {F,E} requires a total of 10 assertions/cubes in total instead of 14

### Merged Karnaugh Map for {F,U}:

```
            RF
          │ 00  01  11  10
       ───┼────────────────
   WAE 000│⎛  ⎞⎛f ⎞⎝      ⎠
       001│⎜  ⎟⎜xx⎟⎛xx   u⎞
       011│⎜  ⎟⎜xx⎟⎝xx   u⎠
       010│⎝  ⎠⎝f ⎟⎛      ⎞
       110│⎛f  ⎛f ⎟ f ⎞⎛  ⎞
       111│⎝f  ⎜xx⎟ xx⎟⎜  ⎟
       101│⎛  ⎞⎜xx⎟ xx⎟⎜  ⎟
       100│⎜  ⎟⎝f ⎠ f ⎠⎝  ⎠
            FU
```

NOTE: xx in the table above refers to the fact that full and empty can never occur at the same time.

```
   F'U' = R'F'W' + R'F'A' + W'E'R + RF'W
   F U' = R'F + WAR' + WF
   F'U  = RW'E
   F U  = 0 (never occurs)
```

The merged Karnaugh Map for {F,U} requires a total of 8 assertions/cubes in total instead of 11

### Merged Karnaugh Map for {F,E,O,U}:

```
            RBE
          │ 000 001 011 010 110 111 101 100
       ───┼─────────────────────────────────
   WAF 000│ 1  ⎠⎛ e 9 e⎞⎝   ⎛Ae⎞⎛ue  ue⎞⎛2 ⎞
       001│ ⎛f  ⎜x   x ⎟ f ⎞⎜ e⎟⎜x B x ⎟⎜  ⎟
       011│ ⎝f 5⎜x   x ⎟ f ⎠⎜ e⎟⎜x   x ⎟⎜  ⎟
       010│    ⎞⎝ e   e⎠⎛   ⎝ e⎠⎝ue  ue⎠⎝  ⎠
       110│ ⎛f   f   f 6 f ⎞   )⎛ e C e⎞(
       111│ ⎛fo  x   x   fo⎞⎛f 7⎜x   x ⎟ f ⎞
       101│ ⎝fo  x   x 8 fo⎠⎝f  ⎜x   x ⎟ f ⎠
       100│ (3             )  4)⎝ e   e⎠(
             FEOU
```

NOTE: The Karnaugh map above contains numbers 0-9 and A-C that count the different cubes and help to more easily track which cube applies to the functions below.

```
      F'E'O'U'= W'F'R'E' + RW'B'E' + WR'A'F' + WE'RF' (1+2+3+4)
      F'E'O'U = 0
      F'E'O U'= 0
      F'E'O U = 0
      F'E O'U'= W'R'E + W'RBE' + WRE                  (9+A+C)
      F'E O'U = W'RE    (B)
      F'E O U'= 0
      F'E O U = 0

      F E'O'U'= W'FR' + WAF'R' + WFR                  (5+6+7)
      F E'O'U = 0
      F E'O U'= WFR'    (8)
      F E'O U = 0
      F E O'U'= 0
      F E O'U = 0
      F E O U'= 0
      F E O U = 0
```

The merged Karnaugh Map for {F,E,O,U} requires a total of 12 assertions/cubes in total instead of 22 (a reduction of 10 assertions!)

## 6.2.5 State Variables as Karnaugh Map Outputs

```
        RW
       │00 01 11 10
      ─┼────────────
   A  0│ F  F
      1│ F  1
         F(t+1)
         4 Assertions

         W
       │ 0  1
      ─┼──────
   R  0│    F
      1│
         O(t+1)
         3 Assertions
```

### Merging Karnaugh Maps with State Variable Outputs FEOU(t+1)

```
            RB
        │   00    01    11    10
      ──┼─────────────────────────
  WA  00│ ⎛FE00  FE00⎞⎛0E0E  0E0E⎞
      01│ ⎝FE00  FE00⎠⎝0E0E  0E0E⎠
      11│ ⎛F0F0  F0F0⎞⎛FE00  FE00⎞
      10│ ⎝F0F0  F0F0⎠⎝FE00  FE00⎠
           FEOU(t+1)
```

List of assertion needed to cover merging Karnaugh Maps with State Variable Outputs FEOU(t+1) above:

```
{!W & !R} |=> {$stable(F) && $stable(E) && O==0 && U==0};
{!W &  R} |=> {      F==0 && $stable(E) && O==0 && U==E};
{ W & !R} |=> {$stable(F) &&       E==0 && O==F && U==0};
{ W &  R} |=> {$stable(F) && $stable(E) && O==0 && U==0};
```

The Karnaugh Maps with State Variable Outputs FEOU(t+1) requires a total of 4 assertions/cubes in total instead of 22 (a reduction of 18 assertions!!!!!)

