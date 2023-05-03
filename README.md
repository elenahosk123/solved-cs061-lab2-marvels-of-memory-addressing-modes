Download Link: https://assignmentchef.com/product/solved-cs061-lab2-marvels-of-memory-addressing-modes
<br>
Today’s lab will cover some new LC-3 instructions, introducing the marvels of <u>​<em>memory addressing</em></u><em> <u>modes</u></em>​, and some more I/O (input/output),

2      Goals for This Week

<ol>

 <li>New LC-3 instructions using memory addressing modes ​<em>direct</em>​, ​<em>indirect</em>​, and ​<em>relative</em>​.</li>

 <li>Exercises 1 – 4:</li>

</ol>

Practice using the three memory addressing modes;

Building a simple loop using the ​<em>conditional branch instruction</em>​; and  Simple input/output using BIOS routines.

<ol start="3">

 <li>So what now??</li>

</ol>

3.1    New LC-3 Instructions

As you know, in the LC-3, values can be stored in system memory (RAM), and copied from there to the eight general purpose registers named R0 through R7, or in several special purpose registers ​<em>(more about these later on)</em>​.

And as you are beginning to see, <strong><em><u>all</u></em></strong>​ ​ microprocessor instructions involve getting values from one or other of those places, manipulating them in some fashion, and writing the result to another place – so we need a way to describe this process.

From now on, we will use a standard notation to describe all such actions called <strong><u>Register Transfer Notation (RTN)</u></strong><u>​:</u>

<ul>

 <li>(Rn) means “the <u>​<em>contents</em></u>​ of Register n” – i.e the value currently stored in that register.</li>

 <li>Mem[ xnnnn ] means “the <em><u>contents of memory address</u></em><u>​ </u>​ xnnnn”</li>

 <li>← means “copy the <strong><em><u>value</u></em></strong>​ ​ on the right of the arrow to the ​<strong><em><u>location</u></em></strong><u>​</u> on the left”, rather like the</li>

</ul>

C++ assignment operator ‘=’

<em>(the location on the left may be either a </em><u>​<em>memory address</em></u>​<em> or a </em>​<em><u>local register</u></em>​<em>)</em>​, so:

R3 ← Mem[ x3042 ]

means copy the ​<em><u>contents</u></em>​ of memory location x3042 to ​<em><u>Register 3</u></em>​.

Mem[ x3120 ] ← (R6)

means copy the ​<em><u>contents</u></em>​ of Register 6 to the <u>​<em>memory location x3120</em></u>

R3 ← (R1) + (R2)

means add the <u>​<em>contents</em></u>​ of R1 and R2, and write the result to R3.

Also, as we saw last week, a ​<em>label</em>​ is an alias for a memory location (an address), so:

Mem[ myAnswer ] ← (R4)

means copy the ​<em><u>contents</u></em>​ of R4 to the <u>​<em>memory location</em></u>​ corresponding to the ​<em><u>label</u></em>​ myAnswer.

New LC-3 instructions this week:  ST, LDI, STI, LDR, STR, Trap

<em><u>Note:</u></em>​<em>  further details on all LC-3 instructions can be found </em><a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">​</a><a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing"><em>here</em></a><a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">​</a><em> and in Appendix A of the text. </em>

The ​ST (Store ) ​instruction – see the ​<a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">ST tutoria</a><u>​</u><u>l​</u> (Piazza Resources -&gt; LC3 Instructions)

This is the “inverse” of the LD instruction: it stores the contents of a register directly into a location in memory specified by a label <em>(</em>​ <em>overwriting any previous content, of course). </em>

<u>Before we go on to the other memory addressing modes, we need some background.</u>

Consider the instruction:

LD  R1, ADDR_1 ;​ as you now know, this means      ​R1 &lt;-​ Mem[ ADDR_1 ]  <em>Let’s say that ADDR_1 is an alias for memory address x3100;  </em>

<em>and that the value stored at that address is x4A20 = #</em>​<em>18976</em>​<em> = b</em>​<em>0100 1010 0010 0000</em>​  In other words:​  <u>​</u><strong><u>R1 </u></strong><span style="text-decoration: line-through;"><u>←</u></span><strong><u> Mem[ x3100 ]</u></strong>​ ​means​ ​<strong><u>R1 &lt;- x4A20</u></strong>

<strong> </strong>

<em>Now, for reasons that will become clear later on,  the label ADDR_1 </em>​<strong><em>must be no further away from the LD instruction than +/- 256 locations!</em></strong>

<em>if you attempt to LD or ST with a label further away than that, you will get an </em>​<em><u>assembly</u></em><u>​</u><em> error:</em>

<strong>“Instruction references label that cannot be represented in a 9-bit signed PC offset”</strong>​ ​(see ​<a href="https://docs.google.com/document/d/1LFtUGU7jItiOe4gurSzKGiI61YEZ6Y0WsQSXlDwIMaE/edit?usp=sharing">here</a>​).

<em>So we need a mode that gets around that limitiation – and, in fact, we have two such modes. </em>

The ​LDI (​Load Indirect)​ instruction – see the ​<a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">LDI tutorial</a>

This looks a bit like the LD instruction, except that there is one level of ​<em><u>indirection</u></em>​:




LDI  R1, ADDR_1 ; ​loads Mem[ Mem[ADDR_1] ] into R1:

That is, it first fetches Mem[ ADDR_1 ] ​<em>(let’s use ADDR_1 = x3100; and Mem[ADDR_1] = x4A20 again)</em>​.  Then it uses that value as a ​<strong><em>new address</em></strong>​, and fetches Mem[ x4A20 ] ​<em>(i.e. the value </em><u>​<em>stored</em></u>​<em> at x4A20 – let’s say that is x00C4)</em>​, and finally copies that value into R1:

R1 ← Mem[ Mem[ADDR_1]  ]   i.e.  ​<strong>R1 </strong><span style="text-decoration: line-through;">←</span><strong>  Mem[ x4A20 ]</strong>​  i.e.  <u>​</u><strong><u>R1 </u></strong><span style="text-decoration: line-through;"><u>←</u></span><strong> <u>x00C4</u> </strong>




This round-about process is called “​<strong>indirection</strong>​”, and the label is called a “<strong>pointer</strong>​ ” ​ <em>(</em>​ <em>sound familiar?),</em> and it allows us to get around the +/- 256 line limitation ​<em>(although the label itself must still be within that range)</em>







The ​STI (Store Indirect) ​instruction – see the ​<a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">STI tutorial</a> This works just like LDI, only in the opposite direction.

It takes the value from a register and copies (stores) it to memory at the address given by  Mem[ someLabel ] ​<em>(overwriting previous content). </em>

Mem[ Mem[ ADDR_1 ] ] ← (R1)   =&gt;  ​<strong>address x4A20 </strong><span style="text-decoration: line-through;">←</span><strong> (R1)</strong>

​<em>(i.e. the value currently stored in R1 is copied to the address stored in ADDR_1 = x3100, which is x4A20) </em>







The ​LDR (Load Relative) ​instruction – see the ​<a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">LDR tutorial</a>

This uses indirection like LDI, except that it uses a ​<em>register</em>​ as the pointer rather than a memory address:




The instruction

LDR  R1, R6, #0 ; ​<em>For this course, we will </em><u>​<em>always</em></u>​<em> set the offset (the third parameter)  to 0. </em>This copies the value from Mem[ (R6)  + offset 0 ]  into R1:

<em>(let’s say (R6) = x3100, and Mem[ x3100 ] is again x4A20 )</em>

R1 ← Mem[ (R6)+x0 ]  i.e.  ​<strong>R1 </strong><span style="text-decoration: line-through;">←</span><strong> Mem[ x3100 ]</strong>​  i.e.  ​<strong><u>R1 </u></strong><span style="text-decoration: line-through;"><u>←</u></span><strong><u> x4A20</u></strong>







The ​STR (Store Relative) ​instruction – see the ​<a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">STR tutorial</a> This works just like LDR, only in the opposite direction.

In the example, the instruction stores the value from R1 into the address stored in R5 (x3100):




The instruction

STR  R1, R6, #0

copies the value currently stored in R1 into the address stored in R5:

<em>(Again, let’s say (R6) == x3100, as above)</em>

Mem[ (R6) ] ← (R1)   i.e.   ​<strong><u>address x3100 </u></strong><span style="text-decoration: line-through;"><u>←</u></span><strong><u> (R1)</u></strong>




<em>By convention, we usually use </em>​<strong><em>R6</em></strong>​<em> as a “Base Register”, i.e. the pointer register in LDR/STR instructions. </em>




<strong>The </strong>​<strong>Trap </strong>​<strong>instruction</strong>​ –  see Table 1 below and the <u>​</u><a href="https://drive.google.com/folderview?id=0B9AugYlANDZkdkJmUEw1VWFkazQ&amp;usp=sharing">TRAP tutorial</a>​ and p. 543 in the text.




Trap ​instructions are actually calls to a set of ​BIOS subroutines ​(a bit like what C++ calls “functions”). You saw one of them (PUTS) last week. You may use either the “TRAP xnn” form or the alias in your code.




<em>Table 1: Trap instructions </em>

<table width="622">

 <tbody>

  <tr>

   <td width="104"><strong>Invocation </strong></td>

   <td width="67"><strong>Alias </strong></td>

   <td width="451"><strong>Effect </strong></td>

  </tr>

  <tr>

   <td width="104">TRAP x20</td>

   <td width="67">GETC</td>

   <td width="451"><strong>R0</strong>​ ← one character of input (ascii) from the keyboard (no echo)</td>

  </tr>

  <tr>

   <td width="104">TRAP x21</td>

   <td width="67">OUT</td>

   <td width="451">print ​<strong>(R0)</strong>​ to the screen as an ascii character</td>

  </tr>

  <tr>

   <td width="104">TRAP x22</td>

   <td width="67">PUTS</td>

   <td width="451">Jump to memory location ​<strong>(R0)</strong>​ and print the contents of that and each succeeding memory location until a 0 is encountered.<em>Used for printing strings. </em></td>

  </tr>

  <tr>

   <td width="104">TRAP x23</td>

   <td width="67">IN</td>

   <td width="451"><strong><em>You will never use this instruction!</em></strong>Prompt the user to input a single character; get the input from the keyboard &amp; store it in R0, and echo it to the console.</td>

  </tr>

  <tr>

   <td width="104">TRAP x25</td>

   <td width="67">HALT</td>

   <td width="451">Terminates the program</td>

  </tr>

 </tbody>

</table>




<strong> </strong>

<h2>3.2    Exercises 1 – 4</h2>




<strong>REMINDER: Always open the Text Window of your simpl LC-3 emulator!</strong> <em>(by checking the “Text Window” box at the bottom as soon as you open simpl) </em>Keep an eye on it for run-time error messages!




<h1>Exercise 1: Direct memory addressing mode (LD and ST)</h1>




Write a program in lab-02_ex1.asm that uses .FILL pseudo-ops to load the values #65 (decimal 65) and x41 (hexadecimal 41) into two memory locations with the labels DEC_65 and HEX_41 respectively. <em>Check the binary values stored in the two locations – what do you notice?</em>​

Look up an ASCII table ​<em>(use the back of your text or </em><a href="https://www.asciitable.com/">​</a><a href="https://www.asciitable.com/"><em>this table</em></a><a href="https://www.asciitable.com/">​</a><em>)</em>​ to see what these values represent when interpreted as <em><u>characters</u></em>​       ​ rather than <u>​<em>numbers</em></u>​<em>. </em>




Then use the ​<strong>LD</strong>​ instruction to load these two values into registers R3 and R4 respectively. Run the program, and inspect those registers to make sure it did what you expected.







<h1>Exercise 2: Indirect memory addressing mode (LDI and STI)</h1>




Sometimes, the data we need is located in some remote region of memory.

Copy the program from exercise 1 into your lab-02_ex2.asm file and replace the ​<em><u>data</u></em><u>​</u> stored at DEC_65 and HEX_41 with two far away ​<em><u>addresses</u></em>​ such as x4000 and x4001 respectively, and rename the labels to DEC_65_PTR and HEX_41_PTR ​<em>(because your labels are now pointers to remote data)</em>​.







You can load the data into the new locations by putting the following code at the end of your current data block, ​<u>after</u>​ the HALT instruction, and ​<u>before​</u> the .END pseudo-op:

;; Remote data

.orig x4000 NEW_DEC_65 .FILL #65

NEW_HEX_41 .FILL x41




<strong><em>But now we have a problem!</em></strong>

As we have already seen, the LC-3 Direct memory addressing mode (LD, ST instructions) <u>​only works</u> <u>with labels that are within  +/- #256 memory locations of the instruction</u>​ ​<em>(see </em><a href="https://docs.google.com/document/d/1LFtUGU7jItiOe4gurSzKGiI61YEZ6Y0WsQSXlDwIMaE/edit?usp=sharing">​</a><a href="https://docs.google.com/document/d/1LFtUGU7jItiOe4gurSzKGiI61YEZ6Y0WsQSXlDwIMaE/edit?usp=sharing"><em>here</em></a><a href="https://docs.google.com/document/d/1LFtUGU7jItiOe4gurSzKGiI61YEZ6Y0WsQSXlDwIMaE/edit?usp=sharing">​</a> <em>for full details)</em>​ – so our new data locations are too far away from the code to be accessed with LD and ST, even though we have provided new labels for them.

<strong>Try using the LD instruction with these new labels and see what happens! </strong>




But never fear – the ​<strong>indirect</strong>​ and ​<strong>relative</strong>​ addressing modes will come to the rescue!

Replace the LD instruction with one using ​<strong>LDI</strong>​ to load the data into R3 and R4

<em>(Hint: use the _PTR labels, not the NEW_ ones – in fact you can now remove those, they are of no use!) </em>




Next, add code to increment the values in R3 and R4 by 1.

<em>(What ascii characters do they correspond to now?) </em>

Finally, store the incremented values ​<em><u>back</u></em>​ into addresses x4000 and x4001 using ​<strong>STI</strong>​. Again, inspect the registers to make sure it worked as expected!

<strong> </strong>

<strong> </strong>

<h1>Exercise 3: Relative memory addressing mode (LDR and STR)</h1>




Use your lab02_ex3.asm file

Start with the same setup as in exercise 02: You have two labeled locations (“pointers”), which contain two “remote” memory addresses; your actual data is stored at those remote addresses.




Directly load (​<strong>LD</strong>​) the values of the <u>​<em>pointers</em></u>​ ​<em>(i.e. the actual remote memory addresses)</em>​ into R5 and R6 respectively.

Now, use the relative memory addressing mode (​<strong>LDR</strong>​) to load the remote data into R3 and R4, using R5 and R6 as “Base Registers” – i.e. the registers that hold the memory pointers.




Perform the same manipulation as in exercise 02 – i.e. increment the values in R3 &amp; R4, then store those incremented values back into x4000 and x4001, this time using ​<strong>STR</strong>​ ​<em>(inspect your registers to confirm, as always)</em>​.




<strong><u>SUCCESS!!</u> </strong>




You have now used the two memory addressing modes <u>​indirect</u>​ ​<em>(LDI, STI)</em>​ and ​<u>relative​</u> ​<em>(LDR, STR)</em>​ to accomplish exactly the same goal – each uses indirection (“pointers”) to access memory locations that were too far away to be reached by LD:




The ​<u>Indirect​</u> memory addressing mode uses a ​<em><u>label</u></em>​ (aliased memory location) as a pointer.

The ​<u>Relative​</u> memory addressing mode uses a ​<em><u>register</u></em>​ containing a memory address as a pointer.

<em>(The downside of both is that they require one more memory read than direct addressing).</em><strong>                           </strong>

<h1>Exercise 4: Loops</h1>




Use the conditional branch instruction (​<strong>BR</strong>​) to construct a ​<em><u>counter-controlled loop</u></em><u>​</u> ​<em>(the same control structure you learned in lab 1 and assn 1) </em>




Hard-code ​<em>(i.e. use .FILL)</em>​ local data values x61 and x1A, and load them into R0 and R1 respectively. Inside a loop, output to console the contents of R0 (Trap x21, or OUT), then immediately increment R0 by 1.

Use R1 as the loop counter by decrementing it by 1 each iteration, just as you did in lab 1 – i.e. the loop should be executed exactly x1A times.

Think carefully about how and when to terminate the loop: do you use BRn or BRnz or BRz or BRzp or ??




<em>What does this loop do? Can you figure it out </em>​<strong><em><u>before</u></em></strong>​<em> you run it? </em>


