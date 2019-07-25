---
title: "WebAssembly"
fullTitle: "WebAssembly"
name: "Mikhail Ignatovich"
date: 2018-07-19
external_link: ""
draft: false
---

One of the supported actors in [Manifest](/documentation/manifest) is WebAssembly (WASM). To gain support as an actor in Kolmonet, WASM must support the special interface (listed below).
The implemented sequence of calls is:

- For each argument:
    - Reserve memory for the argument name (_set_arg_name)
    - Set to reserved memory name of the argument
    - Get argument's index (_get_arg_index)
    - Reserve memory for the argument's value (_set_arg)
    - Set to reserved memory argument's value
- Execute WASM algorithm (_exec)
- Get result's pointer and size (_get_result, _get_result_size)
- If any errors occur get error's pointer and size to get an error message(_get_last_error, _get_last_error_size)

**WASM interface (Rust):**
```Rust
_set_arg_name(arg_handle: u8, size: usize) -> *mut c_void
_get_arg_index(arg_handle: u8) -> u8
_set_arg(arg_index: u8, size: usize) -> *mut c_void
_get_result() -> *mut c_void 
_get_result_size() -> usize 
_get_last_error() -> *mut c_void 
_get_last_error_size() -> usize 
_exec() -> bool 
```

**WASM interface (c):**
```c
int* _set_arg_name(uint8_t arg_handle, int size)
uint8_t _get_arg_index(uint8_t arg_handle)
int* _set_arg(uint8_t arg_index, int size)
int* _get_result()
int _get_result_size()
int* _get_last_error()
int _get_last_error_size()
int _exec()
```

## _set_arg_name

This method reserve memory inside WASM for receiving the parameter's name. Parameter identified by its handle. Later, parameter handle will use for getting parameter index by calling _get_arg_index.
The goal of call sequence _set_arg_name -> _get_arg_index is to get parameter index by parameter's name. It allows naming parameters in Manifest in a human readable form.

Parameters:
- arg_handle - parameter's handle
- size - size in bytes for name of parameter

Return value:
- Pointer to memory inside WASM (null if error)


## _get_arg_index

This method allows getting parameter index by parameter's handle. Parameter index will be used later for call _set_arg.

Parameters:
- arg_handle - parameter's handle

Return value:
- Argument index (0 if error)


## _set_arg

This method reserve memory inside WASM for receiving the parameter's value.

Parameters:
- arg_index - parameter's index
- size - size in bytes for value of parameter

Return value:
- Pointer to memory inside WASM (null if error)


## _get_result

This method return pointer to memory containing the result of WASM execution.

Return value:
- Pointer to memory inside WASM (null if error)


## _get_result_size

This method return size of memory containing the result of WASM execution.

Return value:
- Size of memory inside WASM (0 if execution error occurs or has no executed yet)


## _get_last_error

This method return pointer to memory containing error message of last WASM unsuccessful call.

Return value:
- Pointer to memory inside WASM (null if error)


## _get_last_error_size

This method return size of memory containing error message of last WASM unsuccessful call.

Return value:
- Size of memory inside WASM (0 if no error was detected)


## _exec

This method make execution of WASM algorithm. All arguments of the algorithm must be initialized before calling.

Return value:
- Result of execution (true if succeeded, false if execution failed)
