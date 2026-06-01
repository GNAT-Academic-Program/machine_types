# machine_types

Foundational machine-level types for bare-metal Ada applications.

## Overview

`machine_types` provides a comprehensive set of unsigned integer types with precise bit widths for embedded systems programming. It serves as a common dependency for hardware abstraction layers, enabling consistent type usage across peripheral drivers and register definitions.

## Features

- Complete set of unsigned integer types from 1 to 64 bits
- Precise size specifications for hardware register mapping
- Pure package for maximum optimization and reusability
- Zero dependencies beyond standard Ada Interfaces
- Consistent naming convention (`UInt1` through `UInt64`)

## API

### MT Package

```ada
with Interfaces;

package MT is
   pragma Pure;

   type Bit is mod 2**1 with Size => 1;
   type UInt2 is mod 2**2 with Size => 2;
   type UInt3 is mod 2**3 with Size => 3;
   type UInt4 is mod 2**4 with Size => 4;
   type UInt5 is mod 2**5 with Size => 5;
   type UInt6 is mod 2**6 with Size => 6;
   type UInt7 is mod 2**7 with Size => 7;
   subtype UInt8 is Interfaces.Unsigned_8;
   type UInt9 is mod 2**9 with Size => 9;
   type UInt10 is mod 2**10 with Size => 10;
   -- ... continues through UInt64
   subtype UInt16 is Interfaces.Unsigned_16;
   subtype UInt32 is Interfaces.Unsigned_32;
   subtype UInt64 is Interfaces.Unsigned_64;
end MT;
```

## Usage

### In Generic Peripheral Interfaces

```ada
with MT;

package Gpio_Types is
   type Logic_Level is (Low, High);
   
   function To_Bit (Level : Logic_Level) return MT.Bit is
     (case Level is
         when Low  => 0,
         when High => 1);
   
   function From_Bit (B : MT.Bit) return Logic_Level is
     (if B = 0 then Low else High);
end Gpio_Types;
```

### In Driver Implementations

```ada
with MT;

package body STM32G431_GPIO is
   function Driver_Read (P : Pin) return MT.Bit is
      Port : GPIO_Port renames Get_Port (P);
      Mask : constant MT.UInt16 := Shift_Left (1, Get_Pin_Number (P));
   begin
      if (Port.IDR and Mask) /= 0 then
         return 1;
      else
         return 0;
      end if;
   end Driver_Read;
end STM32G431_GPIO;
```

## Design Rationale

### Pure Package

The `pragma Pure` directive allows the package to be used in any context, including other pure packages, and enables maximum compiler optimization through constant folding and elimination.

### Comprehensive Type Coverage

Providing types for all bit widths from 1 to 64 eliminates the need for each project to define its own machine types, ensuring consistency across the ecosystem.

### Standard Subtypes for Common Sizes

Using subtypes of `Interfaces.Unsigned_8`, `Unsigned_16`, `Unsigned_32`, and `Unsigned_64` for standard sizes ensures compatibility with existing Ada code and libraries while maintaining naming consistency.

### Explicit Size Specifications

Each type includes an explicit `Size` aspect, guaranteeing the compiler will reject any platform where the type cannot be represented with the specified bit width.


## Integration

Add to your `alire.toml`:

```toml
[[depends-on]]
machine_types = "^0.1.0"
```

## Dependencies

None (only standard Ada `Interfaces` package)


## License

MIT OR Apache-2.0 WITH LLVM-exception
