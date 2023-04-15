# 6502C Addressing Modes Explained

## **Implied (Implicit)**

- no address involved. Operation does not need one. Operates on registers or stack alone.
- examples
  - ++ RTS :: returns from subroutine
  - ++ INX :: increments the value in the X register
  - ++ CLC :: clear the carry flag in the status register
  
## **Accumulator**

- no address involved. Acts on accumulator alone.
- examples
  - ++ ASL :: arithmetic accumulator shift left
  - ++ ROR :: rotate accumulator right

## **Immediate**


- no address involved, the argument is a simple value.
- examples
  - ++ LDA #$01, load the accumulator with the value of 1, not from address $0001.
  - ++ CMP #$FA, compare the value in the accumulator with the value in $FA.

## **Absolute**

- argument is a 16-bit address somewhere in memory.
- examples
  - ++ LDA $40FA: load the accumulator with the value at address $40FA.
  - ++ JMP $2001: jumpt to the subroutine at memory location $2001.

## **Zero Page**

- argument is a location in zero-page memory.
- $FA is the same location as $00FA, except treated special, faster, and takes less than one byte.
  ++ caveat: zero-page can be moved around. (maybe...)
- examples
  - ++ LDA $FA: load accumulator with the value located at $FA
  - ++ STX $80: store the value of the X register into the address location at $80.
  
## **Relative**

- address is relative to the instruction's position in memory
- assembly language instruction looks like absolute addressing,
  - but assembler/monitor converts it to relative.
- examples
  - ++ BNE $21E4 :: if the zero flag is set, jump to location $21E4
  - ++ BCS $138F :: if the carry flag is set, jumpt to location $138F

## **Indexed Absolute**

- an absolute address into memory, with the value of an index register added to it first
- examples
  - ++ LDA $2000, X :: loads the accumulator with the value located at $2000 + X
    - for instance, if X == 8 this is equivalent to LDA $2008 in absolute addressing.
  - ++ INC $133E, Y:: if Y == 2, increment the value at memory location $1340 = ($133E + 2)

## **Index Zero-Page**

- like Index Absolute, but.... In Zero Page! :-)
- caveat: Y an only be used as the index register with STX and LDX.
- examples
  - ++ LDA \$FA, X :: if X == 2, the equivalent of LDA $FC ($FA + 2)
  - ++ STX \$80, Y :: if Y == 9, store the value in the X register at zero-page location $89.

## **Absolute Indirect**

- get the value from the address shown, and the next byte following it, and use them as the actual address to jump to.
- note: this mode can only be used with JMP.
- note: the two bytes of the address are in little endian order. low-byte first.
- examples
  - ++ JMP ($2000) $2000 holds FA, $2001 holds 13
    - turns into JMP $13FA

- caveat: never use this on a page boundary. Like this...
- ++ JMP ($20FF)
  - that (should) get the byte from $20FF and then get the high byte from $2100, but what really happens is a wrap around. It gets the high byte from $2000, so it does not go up a page.

## **Indexed Indirect**

- first of all, this is very rarely used
- add X to the zero page value, use that new address as the low byte of an indirect pointer into memory.
- examples
  - ++ LDA ($80, X) X == 4, $84 in zero page holds $20, &85 in zero page holds $40
  - add X to the value: $80 + 4 = $84
  - get the lowe byte from $84: $20
  - get the high byte from the next location: $85: $40
  - make an address from those two values, $4020
  - do the operation on that address: LDA $4020

## **Indirect Indexed**

- this one is much more commonly used.
- form an address from the value at the (address), then add Y to that value, and perform the operation on that address.
- example
- ++ LDA ($80), Y: Y == 4, $80 in zero page holds $20, $81 in zero page holds $40
  - get the low byte from the zero-page address ($80) given: $20
  - get the high byte from the next location in zero-page ($81): which is $40
  - form an address from those two values: $4020
  - add the value in Y to that address: $4024
  - do the operation on that address: LDA $4024
  