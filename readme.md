# basic stm32 project test / template

this is the bare minimum I needed to get a STM32 project set up that I can edit from vscode and easily flash onto my chip.

steps:
* use stm32cubemx to set up your board.
* generate code, for "toolchain/IDE" use "cmake". (under the "project manager" tab)
* ensure the following software is installed (and added to path): `cmake`, `arm-none-eabi-gcc`
* i also had to edit the generated `STM32F401XX_FLASH.ld` file. idk why, or if you also will have to do it, but if so, this is what i had to do:
    * find all occurences of `(READONLY)`
    * delete said occurences
    * ex: `.ARM.extab (READONLY) : ` -> `.ARM.extab : `
* then setup build profile with `cmake --preset Debug`
* then you can build using `cmake --build --preset Debug`. the built elf file will appear in `build/Debug`
* flash the elf file to the chip using `STM32_Programmer_CLI -c port=swd -w build/Debug/<project name>.elf -v -rst`
    * the `port=swd` specifies to use the serial for debugging, i.e. using a ST LINK
    * `-rst` makes the device reset after being flashed. otherwise nothing will happen after you flash it.

to compile and flash, do: `cmake --build --preset Debug;STM32_Programmer_CLI -c port=swd -w build/<project name>.elf -v -rst`

## getting vscode clangd language server working

i had to add the .vscode/settings.json file pointing clangd to `arm-none-eabi-gcc`. it should look something like: 
```json
{
    "clangd.arguments": [
        "--query-driver=\"C:/Program Files (x86)/GNU Arm Embedded Toolchain/10 2021.10/bin/arm-none-eabi-gcc.exe\""
    ]
}
```

i also had to create a .clangd file. i used [hx4stm](https://github.com/erdetn/hx4stm) for this. it wasnt really made for windows so had to tweak some things for it to run.

it should look something like:
```yaml
#.clangd
CompileFlags:
	Add: [ -IC:/Users/USER/STM32CubeIDE/workspace_2.1.1/wafgegrghrghthfghf/Drivers/STM32F4xx_HAL_Driver/Inc, -IC:/Users/USER/STM32CubeIDE/workspace_2.1.1/wafgegrghrghthfghf/Drivers/STM32F4xx_HAL_Driver/Inc/Legacy, -IC:/Users/USER/STM32CubeIDE/workspace_2.1.1/wafgegrghrghthfghf/Drivers/CMSIS/Device/ST/STM32F4xx/Include, -IC:/Users/USER/STM32CubeIDE/workspace_2.1.1/wafgegrghrghthfghf/Drivers/CMSIS/Include, -IC:/Users/USER/STM32CubeIDE/workspace_2.1.1/wafgegrghrghthfghf/Core/Inc, -IC:/Users/USER/STM32CubeIDE/workspace_2.1.1/wafgegrghrghthfghf/
 ]
```

this made the LSP mostly work. you can go to definitions and what not. i still get like two errors in main.c but its 3am now and i have sat trying to get this working for hours so i think its good enough. 