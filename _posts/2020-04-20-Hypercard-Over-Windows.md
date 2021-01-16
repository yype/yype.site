---
layout: post
title: PlaidCTF2020 The Watness 2 Write-up
description: 
summary: 
tags: [Write-up, Reverse Engineering, Hypercard, PlaidCTF, PlaidCTF2020]
---

## Intro

Recently in PlaidCTF2020 there was a challenge called *The Watness 2*, which is a game that requires *HyperCard* to run. Since I did not have a Macbook computer, I'd been struggling figuring out ways to run this game over my Windows 10 laptop. Here is how I finally managed to do that.

## Steps

- Follow [this great video tutorial](https://www.youtube.com/watch?v=TY3pjSGg1y4)
- Download [StuffitExpander](https://www.macintoshrepository.org/2475-stuffit-expander-and-dropstuff-5-5), add it to the volumes' list, install it inside the VM:

![1587369699146]({{ site.github.url }}/assets/HypercardOverWindows/1587369699146.png)

- Download [HyperCard 2.4](https://macintoshgarden.org/apps/hypercard-241), install it inside the VM as described above
- Extract the .rc1 file from the .sit file and open it with a simple double-click:

![1587369949569]({{ site.github.url }}/assets/HypercardOverWindows/1587369949569.png)

### Challenge Solution

Extract the stack's script code:

![1587389434804]({{ site.github.url }}/assets/HypercardOverWindows/1587389434804.png)

```
on openCard
  Send colorMe to this card
  pass openCard
end openCard

on closeCard
  global prev_card
  get the id of this cd
  put it into prev_card
  lock screen
  pass closeCard
end closeCard

on colorMe
  AddColor colorCard,stamp,0
end colorMe

on openStack
  AddColor install
  setupMenu
  go to card "tun-1-n"
  pass openStack
end openStack

on closeStack
  AddColor remove
  pass closeStack
end closeStack

on genPuzzle
  send "doMenu New Button" to Hypercard
end genPuzzle

on initCard
  answer prev_card
end initCard

on menuReset
  setupMenu
  pass menuReset
end menuReset


on setupMenu
  if there is not a menu "Watness" then
    create menu "Watness"
    put "Generate Puzzle"&return&"Init Card"&return&"Set up Nav"&return&"Create Puzzle" into menu "Watness" with menuMsg genPuzzle,initCard,setupNav,constructPuzzle
  end if
end setupMenu


on setupNav
  ask "What is the name of this card"
  set name of this cd to it
  
  ask "Where should the left go?"
  put it into left_loc
  send "doMenu New Button" to Hypercard
  set height of the last button to 342
  set width of the last button to 100
  set topleft of the last button to "0,0"
  set style of the last button to "transparent"
  set name of the last button to ""
  put "on mouseUp"&return&"go to card "&quote&""&left_loc&quote&return&"end mouseUp" into left_script
  set script of last button to left_script
  
  ask "Where should the right go?"
  put it into right_loc
  send "doMenu New Button" to Hypercard
  set height of the last button to 342
  set width of the last button to 100
  set topleft of the last button to "412,0"
  set style of the last button to "transparent"
  set name of the last button to ""
  put "on mouseUp"&return&"go to card "&quote&""&right_loc&quote&return&"end mouseUp" into right_script
  set script of last button to right_script
  
  ask "Where should fwd go?"
  put it into fwd_loc
  send "doMenu New Button" to Hypercard
  set height of the last button to 342
  set width of the last button to 311
  set the top of the last button to 0
  set the left of the last button to 100
  set style of the last button to "transparent"
  set name of the last button to ""
  put "on mouseUp"&return&"go to card "&quote&""&fwd_loc&quote&return&"end mouseUp" into fwd_script
  set script of last button to fwd_script
  
  get the script of this card
  put it into cd_script
  put cd_script&return into cd_script
  put cd_script&"on arrowKey key"&return into cd_script
  put cd_script&" if key = "&quote&"left"&quote&"then"&return into cd_script
  put cd_script&"  go to cd "&quote&left_loc&quote&return into cd_script
  put cd_script&" end if"&return into cd_script
  put cd_script&" if key = "&quote&"right"&quote&"then"&return into cd_script
  put cd_script&"  go to cd "&quote&right_loc&quote&return into cd_script
  put cd_script&" end if"&return into cd_script
  put cd_script&" if key = "&quote&"up"&quote&"then"&return into cd_script
  put cd_script&"  go to cd "&quote&fwd_loc&quote&return into cd_script
  put cd_script&" end if"&return into cd_script
  put cd_script&"end arrowKey"&return into cd_script
  
  
  set the script of this cd to cd_script
end setupNav


on makeNode
  global node
  send "doMenu New Button" to Hypercard
  put the id of the last button into node
end makeNode


on constructPuzzle
  global node,constraints
  
  ask "What are the constraints"
  put it into constraints
  
  get the script of this cd
  put it into cd_script
  put cd_script&return into cd_script
  put cd_script&"on openCard"&return into cd_script
  put cd_script&"  global constraints,path,cursor_x,cursor_y," into cd_script
  
  put 0 into i
  repeat for 8
    
    put 0 into j
    repeat for 8
      put cd_script&"active_"&i&"_"&j&"," into cd_script
      
      put j+1 into j
    end repeat
    
    put i+1 into i
  end repeat
  
  put cd_script&"dummy"&return into cd_script
  put cd_script&"  colorme"&return into cd_script
  put cd_script&"  put -1 into cursor_x"&return into cd_script
  put cd_script&"  put 0 into cursor_y"&return into cd_script
  put cd_script&"  put "&quote&quote&" into path"&return into cd_script
  put cd_script&"  put "&quote&constraints&quote&" into constraints"&return into cd_script
  
  put 1 into c_i
  put 0 into i
  repeat for 7
    
    put 0 into j
    repeat for 7
      get char (j*7+i+1) of constraints
      put it into letter
      
      if letter <> " " then
        makeNode
        
        set the width of button id node to 10
        set the height of button id node to 10
        
        set the top of button id node to (76 + j * 30)
        set the left of button id node to (161 + i * 30)
        
        set the style of button id node to "opaque"
        set showName of button id node to false
        
        if letter = "r" then
          put "65535,0,0" into node_color
        end if
        if letter = "g" then
          put "0,65535,0" into node_color
        end if
        if letter = "b" then
          put "0,0,65535" into node_color
        end if
        
        if letter <> " " then
          addColor colorButton, cd, node, node_color
          
          put cd_script&"  addColor colorButton, cd, "&node&", "&quote&node_color&quote&return into cd_script
        end if
      end if
      
      
      
      put j+1 into j
    end repeat
    
    put i+1 into i
  end repeat
  
  
  
  put 0 into i
  repeat for 8
    
    put 0 into j
    repeat for 8
      put cd_script&"  put "&quote&quote&" into active_"&i&"_"&j&return into cd_script
      
      put j+1 into j
    end repeat
    
    put i+1 into i
  end repeat
  
  makeNode
  
  set the width of button id node to 10
  set the height of button id node to 15
  set the left of button id node to 356
  set the top of button id node to 276
  set the name of button id node to "path_extension"
  set showName of button id node to false
  set the style of button id node to opaque
  addcolor colorButton, cd, node, "37632,30208,12288"
  
  put "" into node_script
  put node_script&"on checkYoSelf"&return into node_script
  put node_script&"  addcolor colorButton, cd, "&node&", "&quote&"65535,65535,30000"&quote&return into node_script
  put node_script&"end checkYoSelf"&return into node_script
  
  set the script of button id node to node_script
  
  put cd_script&"  addcolor colorButton, cd, "&node&", "&quote&"37632,30208,12288"&quote&return into cd_script
  
  
  makeNode
  
  set the width of button id node to 10
  set the height of button id node to 10
  set the left of button id node to 356
  set the top of button id node to 286
  set the name of button id node to "finale"
  set showName of button id node to false
  set the style of button id node to oval
  addcolor colorButton, cd, node, "37632,30208,12288"
  
  put "" into node_script
  put node_script&"on mouseUp"&return into node_script
  put node_script&"  global cursor_x, cursor_y"&return into node_script
  put node_script&"  if (cursor_x = 7) and (cursor_y = 7) then"&return into node_script
  put node_script&"    addcolor colorButton, cd, "&node&", "&quote&"65535,65535,30000"&quote&return into node_script
  put node_script&"    send "&quote&"checkYoSelf"&quote&" to button path_extension"&return into node_script
  put node_script&"    send "&quote&"checkSolution"&quote&" to this cd"&return into node_script
  put node_script&"  end if"&return into node_script
  put node_script&"end mouseUp"&return into node_script
  
  set the script of button id node to node_script
  
  put cd_script&"  addcolor colorButton, cd, "&node&", "&quote&"37632,30208,12288"&quote&return into cd_script
  
  put 0 into i
  repeat for 7
    
    put 0 into j
    repeat for 8
      makeNode
      
      set the width of button id node to 30
      set the height of button id node to 10
      
      set the top of button id node to (61 + 30 * j)
      set the left of button id node to (151 + 30 * i)
      
      set the style of button id node to opaque
      set the name of button id node to "h_path_"&i&"_"&j
      
      set showName of button id node to false
      
      addcolor colorButton, cd, node, "37632,30208,12288"
      
      put cd_script&"  addcolor colorButton, cd, "&node&", "&quote&"37632,30208,12288"&quote&return into cd_script
      
      get the script of button id node
      put it into node_script
      
      put "active_"&i&"_"&j into f_node
      put "active_"&(i+1)&"_"&j into s_node
      
      put node_script&return into node_script
      put node_script&"on checkYoSelf"&return into node_script
      put node_script&"  global "&f_node&","&s_node&return into node_script
      put node_script&"  if ("&f_node&" = true) and ("&s_node&" = true) then"&return into node_script
      put node_script&"    addcolor colorButton, cd, "&node&", "&quote&"65535,65535,30000"&quote&return into node_script
      put node_script&"  end if"&return into node_script
      put node_script&"end checkYoSelf"&return into node_script
      
      set the script of button id node to node_script
      
      put j+1 into j
    end repeat
    
    put i+1 into i
  end repeat
  
  put 0 into i
  repeat for 8
    
    put 0 into j
    repeat for 7
      makeNode
      
      set the width of button id node to 10
      set the height of button id node to 30
      
      set the top of button id node to (66 + 30 * j)
      set the left of button id node to (146 + 30 * i)
      
      set the style of button id node to opaque
      set the name of button id node to "v_path_"&i&"_"&j
      
      set showName of button id node to false
      
      addcolor colorButton, cd, node, "37632,30208,12288"
      
      put cd_script&"  addcolor colorButton, cd, "&node&", "&quote&"37632,30208,12288"&quote&return into cd_script
      
      get the script of button id node
      put it into node_script
      
      put "active_"&i&"_"&j into f_node
      put "active_"&i&"_"&(j+1) into s_node
      
      put node_script&return into node_script
      put node_script&"on checkYoSelf"&return into node_script
      put node_script&"  global "&f_node&","&s_node&return into node_script
      put node_script&"  if ("&f_node&" = true) and ("&s_node&" = true) then"&return into node_script
      put node_script&"    addcolor colorButton, cd, "&node&", "&quote&"65535,65535,30000"&quote&return into node_script
      put node_script&"  end if"&return into node_script
      put node_script&"end checkYoSelf"&return into node_script
      
      set the script of button id node to node_script
      
      put j+1 into j
    end repeat
    
    put i+1 into i
  end repeat
  
  put 0 into i
  repeat for 8
    
    put 0 into j
    repeat for 8
      makeNode
      
      set the width of button id node to 10
      set the height of button id node to 10
      
      set the top of button id node to (61 + 30 * j)
      set the left of button id node to (146 + 30 * i)
      
      set the style of button id node to oval
      set the name of button id node to "button_"&i&"_"&j
      
      set showName of button id node to false
      
      addcolor colorButton, cd, node, "37632,30208,12288"
      
      put "active_"&i&"_"&j into v_name
      
      put "" into node_script
      put node_script&"on mouseUp"&return into node_script
      put node_script&"  global "&v_name&",cursor_x,cursor_y"&return into node_script
      put node_script&"  put cursor_x into prev_x"&return into node_script
      put node_script&"  put cursor_y into prev_y"&return into node_script
      put node_script&"  put abs(cursor_x-"&i&") into dx"&return into node_script
      put node_script&"  put abs(cursor_y-"&j&") into dy"&return into node_script
      put node_script&"  if ("&v_name&" = "&quote&quote&") and ((dx = 1 and dy = 0) or (dx = 0 and dy = 1)) then"&return into node_script
      put node_script&"    put true into "&v_name&return into node_script
      put node_script&"    send "&quote&"updateState "&i&","&j&quote&" to this cd"&return into node_script
      put node_script&"    addcolor colorButton, cd, "&node&", "&quote&"65535,65535,30000"&quote&return into node_script
      put node_script&"  end if"&return into node_script
      put node_script&"end mouseUp"&return into node_script
      
      set the script of button id node to node_script
      
      put cd_script&"  addcolor colorButton, cd, "&node&", "&quote&"37632,30208,12288"&quote&return into cd_script
      
      put j+1 into j
    end repeat
    
    put i+1 into i
  end repeat
  
  set the width of button button_0_0 to 30
  set the height of button button_0_0 to 30
  
  set the top of button button_0_0 to 51
  set the left of button button_0_0 to 136
  
  get the id of button button_0_0
  addColor colorButton, cd, it, "37632,30208,12288"
  
  put cd_script&"end openCard"&return into cd_script
  set the script of this cd to cd_script
  
end constructPuzzle

on checkSolution
  global puzzle_id,path,constraints,flag_1,flag_2,flag_3
  watnesssolver constraints,path
  put the result into success
  if success = "true" then
    if puzzle_id = 1 then
      decoder path,"clrtffxpry"
      put the result into flag_1
    end if
    if puzzle_id = 2 then
      decoder path,"nyghq7xksg"
      put the result into flag_2
    end if
    if puzzle_id = 3 then
      decoder path,"ppyyvn}1{7"
      put the result into flag_3
    end if
  else
    send opencard to this cd
  end if
end checkSolution


on updateState i,j
  global path,cursor_x,cursor_y
  
  if (i <> 0) or (j <> 0) then
    if (cursor_y = j+1) and (cursor_x = i) then
      put path&"U" into path
    end if
    if (cursor_y = j) and (cursor_x = i - 1) then
      put path&"R" into path
    end if
    if (cursor_y = j-1) and (cursor_x = i) then
      put path&"D" into path
    end if
    if (cursor_y = j) and (cursor_x = i + 1) then
      put path&"L" into path
    end if
  end if
  
  
  if cursor_x >= 0 and cursor_y >= 0 then
    put "h_path_"&min(cursor_x, i)&"_"&min(cursor_y, j) into h_path
    put "v_path_"&min(cursor_x, i)&"_"&min(cursor_y, j) into v_path
    
    if i = cursor_x then
      send checkYoSelf to button v_path
    end if
    
    if j = cursor_y then
      send checkYoSelf to button h_path
    end if
    
  end if
  
  put i into cursor_x
  put j into cursor_y
end updateState
```

The card's script code can also be extracted (puzzle 1):

```

on openCard
  global puzzle_id,constraints,path,cursor_x,cursor_y,active_0_0,active_0_1,active_0_2,active_0_3,active_0_4,active_0_5,active_0_6,active_0_7,active_1_0,active_1_1,active_1_2,active_1_3,active_1_4,active_1_5,active_1_6,active_1_7,active_2_0,active_2_1,active_2_2,active_2_3,active_2_4,active_2_5,active_2_6,active_2_7,active_3_0,active_3_1,active_3_2,active_3_3,active_3_4,active_3_5,active_3_6,active_3_7,active_4_0,active_4_1,active_4_2,active_4_3,active_4_4,active_4_5,active_4_6,active_4_7,active_5_0,active_5_1,active_5_2,active_5_3,active_5_4,active_5_5,active_5_6,active_5_7,active_6_0,active_6_1,active_6_2,active_6_3,active_6_4,active_6_5,active_6_6,active_6_7,active_7_0,active_7_1,active_7_2,active_7_3,active_7_4,active_7_5,active_7_6,active_7_7,dummy
  colorme
  put 1 into puzzle_id
  put -1 into cursor_x
  put 0 into cursor_y
  put "" into path
  put "rbrr rgb rb  r brgrbrgb  grrgbbg grg bgrg  bbgrbg" into constraints
  addColor colorButton, cd, 1, "65535,0,0"
  ...
end openCard

on arrowKey key
  if key = "left"then
    go to cd "entry-3-n"
  end if
  if key = "right"then
    go to cd "entry-3-n"
  end if
  if key = "up"then
    go to cd ""
  end if
end arrowKey

```

Now we get the constraint string of this puzzle `"rbrr rgb rb  r brgrbrgb  grrgbbg grg bgrg  bbgrbg"`, these constraints, along with the path that goes to the lower right corner, are passed into one thing called *XCMD* which checks the path's correctness natively (it contains binary instructions that directly run over the 68k CPU). There are 2 XCMD binaries which can be extracted by [this tool](https://github.com/PierreLorenzi/HyperCardPreview), which seem can only run under MacOS. So I'm using the extracted binary from [this](https://ctf.harrisongreen.me/2020/plaidctf/the_watness_2/) great post for now instead.

What's more, I found that I can set breakpoints in the script and debug the game, I could even watch the variables on the fly:

![1588514193878]({{ site.github.url }}/assets/HypercardOverWindows/1588514193878.png)

As for the XCMD part, it's basically just a few hours' reversing work. There are no reliable decompilers for the 68k architecture so I have to read the assembly. It wasn't too hard, but I did spend several hours learning the basic concepts of the 68k's instruction set though.

After the reverse engineering work, the watnesssolver's checking methods can be rewritten in Python as:

```python
def build_automaton(constraints: str):
    trans = str.maketrans(' rgb', '0123')
    return [int(c) for c in constraints.translate(trans)]


def choose_empty(r, g, b):
    if g == 0 and b == 0:
        return 0
    if b < g:
        return 2
    else:
        return 3


def choose_red(r, g, b):
    if r != 2 and r != 3:
        return 0
    if b == 0 or g == 0:
        return 0
    return 1


def choose_green(r, g, b):
    if r <= 4:
        if b <= 4:
            if r == 2 or r == 3:
                return 1
            else:
                return 2
        else:
            return 3
    else:
        return 0


def choose_blue(r, g, b):
    if r <= 4:
        if g <= 4:
            if r == 2 or r == 3:
                return 1
            else:
                return 3
        else:
            return 2
    else:
        return 0


def is_red(constraints, x, y):
    if not (x >= 0 and x < 7 and y >= 0 and y < 7):
        return False
    return constraints[x+y*7] == 1


def get_neighbors(constraints, x, y, color):
    sum = 0
    for bias_y in range(-1, 2):
        for bias_x in range(-1, 2):
            if (bias_x != 0 or bias_y != 0) and (y + bias_y >= 0 and y + bias_y < 7) and \
                    (x + bias_x >= 0 and x + bias_x < 7) and constraints[(x+bias_x)+(y+bias_y)*7] == color:
                sum += 1
    return sum


def step_automaton(constraints):
    new_constraints = constraints[:]
    for y in range(7):
        for x in range(7):
            r, g, b = \
                get_neighbors(constraints, x, y, 1), \
                get_neighbors(constraints, x, y, 2), \
                get_neighbors(constraints, x, y, 3)
            if constraints[x+y*7] == 0:
                new_constraints[x+y*7] = choose_empty(r, g, b)
            elif constraints[x+y*7] == 1:
                new_constraints[x+y*7] = choose_red(r, g, b)
            elif constraints[x+y*7] == 2:
                new_constraints[x+y*7] = choose_green(r, g, b)
            elif constraints[x+y*7] == 3:
                new_constraints[x+y*7] = choose_blue(r, g, b)

    return new_constraints


def perform_move(constraints, mem, x, y, d):
    bias_x, bias_y = 0, 0
    if d == 'U':
        bias_x, bias_y = 0, -1
    elif d == 'D':
        bias_x, bias_y = 0, 1
    elif d == 'L':
        bias_x, bias_y = -1, 0
    elif d == 'R':
        bias_x, bias_y = 1, 0

    if not (x+bias_x >= 0 and x+bias_x < 8 and y+bias_y >= 0 and y+bias_y < 8):
        return False, x, y

    min_x = min(x, x+bias_x)
    min_y = min(y, y+bias_y)

    if bias_y == 0:
        if not (is_red(constraints, min_x, y) or is_red(constraints, min_x, y-1)):
            return False, x, y
    else:
        if not (is_red(constraints, x, min_y) or is_red(constraints, x-1, min_y)):
            return False, x, y

    if mem[x+bias_x][y+bias_y] == 1:
        return False, x, y

    mem[x+bias_x][y+bias_y] = 1

    return True, x+bias_x, y+bias_y


def solver(path, constraints):
    x = y = 0
    constraints = build_automaton(constraints)
    mem = [[0 for i in range(8)] for j in range(8)]  # been to or not
    mem[0][0] = 1
    for each in path:
        yes, new_x, new_y = perform_move(constraints, mem, x, y, each)
        if yes:

            if new_x == 7 and new_y == 7:
                # print(f'Path `{path}` is great ' + '!' * 20)
                return True, True
            x, y = new_x, new_y
            constraints = step_automaton(constraints)
        else:
            #print(f'Path `{path}` is bad')
            return False, False
    return True, False

```

Looking around in the game, we'll know that there are 3 puzzles we need to solve. So we simply run 3 DFS searches on these contraints and three unique solutions will be printed out. 

```python
def dfs(depth, path, constraints):
    if depth > 24: # figured out after multiple tests
        return
    mov, end = solver(path, constraints)
    if end == True:
        print(f'Path {path} is ok')
    if mov == False:
        return
    for d in 'LRUD':
        n_path = path + d
        dfs(depth+1, n_path, constraints)

if __name__ == '__main__':
    constraints_stage1 = 'rbrr rgb rb  r brgrbrgb  grrgbbg grg bgrg  bbgrbg'
    constraints_stage2 = 'rbr  bbggrgrggb   bggbb b  b bbrbbgg gbrrbgrbbb g'
    constraints_stage3 = 'rrbrb rg g  bgrbgggr ggrgr gr rg brr  b  bggrbgbb'

    print('Stage1 solution:')
    dfs(0, '', constraints_stage1)
    print('Stage2 solution:')
    dfs(0, '', constraints_stage2)
    print('Stage3 solution:')
    dfs(0, '', constraints_stage3)

```

Output:

```
Stage1 solution:
Path RDDDRURRRDLLDLDRRURRDDDR is ok
Stage2 solution:
Path RDDRURDDDRURULURRDDDDDRD is ok
Stage3 solution:
Path DRDDDDRUURRRULURRDDDDDDR is ok
```

Input these solutions to each puzzle, after that, we could go to the lock-like thing on the white gate to reveal the flag:

![1588514105474]({{ site.github.url }}/assets/HypercardOverWindows/1588514105474.png)

