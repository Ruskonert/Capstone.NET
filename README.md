# Capstone.NET
Capstone.NET is a .NET Framework binding for the Capstone disassembly framework. It is written in C#, supports Capstone 3, and has a friendly and simple type safe object oriented (OO) API that is ridiculously easy to learn and quick to pick up.

## Features
(+) Supports Capstone 3. Only the ARM, ARM64, and X86 architectures are supported right now but more are on the way!

(+) A friendly and simple to use type safe object oriented (OO) API that is ridiculously easy to learn and pick up. I am aware the C# is an object oriented language, the wording **object oriented API** here means it has a well thought out design!

(+) One of the best documented source code you'll find in an open source project, guaranteed or your money back!

## Quick Example
```C#
// Create X86 Disassembler.
//
// Creating the disassembler in a "using" statement ensures that resources get cleaned up automatically
// when it is no longer needed.
using (var disassembler = CapstoneDisassembler.CreateX86Disassembler(DisassembleMode.Bit32)) {
    // Enable Disassemble Details.
    //
    // Enables disassemble details, which are disabled by default, to provide more detailed information on
    // disassembled binary code.
    disassembler.EnableDetails = true;

    // Set Disassembler's Syntax.
    //
    // Make the disassembler generate instructions in Intel syntax.
    disassembler.Syntax = DisassembleSyntaxOptionValue.Intel;

    // Disassemble All Code.
    //
    // ...
    var code = new byte[] { 0x8d, 0x4c, 0x32, 0x08, 0x01, 0xd8, 0x81, 0xc6, 0x34, 0x12, 0x00, 0x00, 0x05, 0x23, 0x01, 0x00, 0x00, 0x36, 0x8b, 0x84, 0x91, 0x23, 0x01, 0x00, 0x00, 0x41, 0x8d, 0x84, 0x39, 0x89, 0x67, 0x00, 0x00, 0x8d, 0x87, 0x89, 0x67, 0x00, 0x00, 0xb4, 0xc6 };
    var instructions = disassembler.DisassembleAll(code);
    
    // Loop Through Each Disassembled Instruction.
    // ...
    foreach (var instruction in instructions) {
        Console.WriteLine("{0:X}: \t {1} \t {2}", instruction.Address, instruction.Mnemonic, instruction.Operand);
        Console.WriteLine("\t Id = {0}", instruction.Id);

        if (instruction.ArchitectureDetail != null) {
            Console.WriteLine("\t Address Size = {0}", instruction.ArchitectureDetail.AddressSize);
            Console.WriteLine("\t AVX Code Condition = {0}", instruction.ArchitectureDetail.AvxCodeCondition);
            Console.WriteLine("\t AVX Rounding Mode = {0}", instruction.ArchitectureDetail.AvxRoundingMode);
            Console.WriteLine("\t Displacement = {0:X}", instruction.ArchitectureDetail.Displacement);
            Console.WriteLine("\t Mod/Rm = {0:X}", instruction.ArchitectureDetail.ModRm);
            Console.WriteLine("\t Operand Count: {0}", instruction.ArchitectureDetail.Operands.Length);

            // Loop Through Each Instruction's Operands.
            //
            // ...
            foreach (var operand in instruction.ArchitectureDetail.Operands) {
                string operandValue = null;
                switch (operand.Type) {
                    case X86InstructionOperandType.FloatingPoint:
                        operandValue = operand.FloatingPointValue.Value.ToString("X");
                        break;
                    case X86InstructionOperandType.Immediate:
                        operandValue = operand.ImmediateValue.Value.ToString("X");
                        break;
                    case X86InstructionOperandType.Memory:
                        operandValue = "-->";
                        break;
                    case X86InstructionOperandType.Register:
                        operandValue = operand.RegisterValue.Value.ToString();
                        break;
                }
                
                Console.WriteLine("\t\t {0} = {1}", operand.Type, operandValue);
                
                // Handle Memory Operand.
                //
                // ...
                if (operand.Type == X86InstructionOperandType.Memory) {
                    Console.WriteLine("\t\t\t Base Register = {0} ", operand.MemoryValue.BaseRegister);
                    Console.WriteLine("\t\t\t Displacement = {0:X} ", operand.MemoryValue.Displacement);
                    Console.WriteLine("\t\t\t Index Register = {0}", operand.MemoryValue.IndexRegister);
                    Console.WriteLine("\t\t\t Index Register Scale = {0}", operand.MemoryValue.IndexRegisterScale);
                    Console.WriteLine("\t\t\t Segment Register = {0}", operand.MemoryValue.SegmentRegister);
                    Console.WriteLine();
                }
            }
        }
    }
}
```

## Requirements
(+) **.NET Framework 4.0/4.5**: Capstone.NET is compiled against the .NET Framework 4.0/4.5.

(+) **Capstone 3 (X86)**: Capstone.NET is compatible **only** with the X86 version of Capstone 3. Please make sure you only use that version before you open an issue. Down the road, I will definitely have support for the X64 version of Capstone. If this is a problem for you, feel free to either open an issue or fork the project and make it compatible with both X86 and X64. Significant changes might be required to deal with structure alignment issues when interfacing with the native Capstone API.

(+) **X86 Compiled Assembly**: Capstone.NET is compiled as an X86 DLL. This, obviously, means you should only reference it from an X86 assembly. The word **should** should be taken with a grain of salt here because the C# compiler will only trigger a warning if you reference it from an X64 assembly but please be aware that Capstone.NET has **not** been tested when referenced from an X64 binary so possible runtime exceptions or errors might occur. If this is a problem for you, feel free to either open an issue or fork the project and make it compatible with both X86 and X64. Significant changes might be required to deal with structure alignment issues when interfacing with the native Capstone API.

## Packaged Installation
The simplest way to get started with Capstone.NET is to grab it from [Nuget](https://www.nuget.org/packages/Gee.External.Capstone). You can also use the Nuget Package Manager in Visual Studio and search for "**Capstone.NET**".

The alternative is to download the latest release package from GitHub. It will contain all the compiled binaries you need to get started.

The latest release will usually be backwards compatible with older releases but as both Capstone.NET and Capstone mature, this might not always be the case. Please make sure to read the release notes for each release before upgrading!

## Upgrading Capstone
The Capstone team is always hard at work at upgrading Capstone. It is entirely possible that the copy of Capstone that is distributed with a Capstone.NET release is out of date when you download it. You can feel free to replace the **capstone.dll** that is distributed with Capstone.NET with the latest copy you download from the Capstone website. However, please make sure it is **always** named **capstone.dll**. Capstone.NET looks for this DLL when it goes to work and if it can't find it, you will get a runtime exception.

There are several important thing to look out for if you decide to replace the **capstone.dll** with one you download from the Capstone website. Failing to consider these points will usually result in undefined runtime behavior. If you replace **capstone.dll** with one you download from the Capstone website, do so at your own risk:

(+) Capstone.NET **only** supports Capstone 3. Please do not try to use any other major version. If you do, a definite runtime exception is guaranteed!

(+) With every minor release of Capstone, the Capstone team usually adds support for new instructions or new APIs. These will not be available in Capstone.NET! New APIs are usually not a problem since they simply will not be exposed in Capstone.NET. New instructions however might cause undefined runtime behavior. Best case scenario, your application will continue to run fine but you'll notice some disassembled instructions do not make sense. Worst case scenario, you'll get a runtime exception because something from the native API could not be translated correctly to the wonderful managed world of the .NET Framework.

(+) If a minor release of Capstone changes the layout of **any** structures, kiss your application goodbye. This means a definite runtime exception. Sorry folks!

## More Examples
Take a look at the unit tests and the example command line application for an example on how to use. It is ridiculously simple!

## Acknowledgments
Greetings to my sensi Mohamed Saher ([@halsten](https://twitter.com/@halsten)) who suggested I take on this project and was a major help in debugging some stucture alignment issues, the Capstone team ([@capstone_engine](https://twitter.com/@capstone_engine)) for all the great work they are doing, and Matt Graeber ([@mattifestation](https://twitter.com/@mattifestation)) who wrote the original binding for Capstone 2 and whose work was a starting point for this project.

Special thanks to John Källén ([@uxmal](https://github.com/uxmal)) for his contribution and for choosing to use Capstone.NET for his binary decompiler, [reko](https://github.com/uxmal/reko).
