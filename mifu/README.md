# mifu - A Friendly FFUF Wrapper

## Overview

`mifu` is a simple bash wrapper for the powerful web fuzzing tool [`ffuf`](https://github.com/ffuf/ffuf). It aims to simplify common `ffuf` usage patterns, provide helpful prerequisite checks, and offer guidance through an enhanced help menu.

It streamlines the process of launching `ffuf` scans by handling some common options automatically and validating inputs.

## Features

* **Simplified Syntax:** Provides a clear basic syntax: `mifu <URL> <Wordlist> [options]`.
* **Auto `-X POST`:** Automatically adds the `-X POST` method flag to the `ffuf` command if POST data (`-d` or `--data`) is provided without an explicit method (`-X`) being set.
* **Prerequisite Checks:**
    * Verifies that the `ffuf` command is installed and executable before attempting to run. Exits with an error if not found.
    * Checks for the presence of the common Seclists directory (`/usr/share/seclists`) and issues a warning if it's missing, as many suggested wordlists rely on it.
* **Two-Tier Help:**
    * Displays a concise usage message for basic errors (e.g., missing arguments).
    * Shows a detailed help menu (`-h` or `--help`) including usage examples for different fuzzing scenarios and wordlist suggestions.
* **Input Validation:** Checks for the presence of the `FUZZ` keyword in the URL and validates the wordlist path.

## Prerequisites

* **`bash`**: Required to run the script (typically available by default on Linux and macOS).
* **`ffuf`**: **Required**. This is the underlying fuzzing tool that `mifu` wraps.
* **`seclists`**: **Recommended**. While not strictly required for `mifu` to run (if you provide full paths to your own lists), it's highly recommended as the help menu's wordlist suggestions assume its presence at `/usr/share/seclists`.

## Installation

1.  **Download/Save:** Save the script content to a file named `mifu`.
2.  **Make Executable:** Open your terminal and run:
    ```bash
    chmod +x mifu
    ```
3.  **Install Prerequisites (Debian/Ubuntu Example):**
    ```bash
    sudo apt update && sudo apt install ffuf seclists
    ```
    *(Adjust the install command based on your Linux distribution or OS if not using apt).*
4.  **(Recommended) Move to a Directory in your PATH:** To run `mifu` directly as a command from any terminal location (without specifying `./mifu`), you need to place the script file in a directory that is listed in your system's `$PATH` environment variable. Common choices include `/usr/local/bin` (for all users) or `~/.local/bin` (just for your user).

    *Example (for all users):*
    ```bash
    sudo mv mifu /usr/local/bin/
    ```
    *Example (for current user only):*
    ```bash
    # Create the directory if it doesn't exist
    mkdir -p ~/.local/bin
    # Move the script
    mv mifu ~/.local/bin/
    # Note: Ensure ~/.local/bin is included in your $PATH.
    # Many modern systems add this automatically. You might need to restart
    # your terminal or source your shell configuration file (e.g., ~/.bashrc, ~/.zshrc).
    ```
    After moving the file, you should be able to execute the tool simply by typing `mifu` in your terminal.

## Usage

The basic syntax is:

```bash
mifu <URL_with_FUZZ> <full_wordlist_path> [ffuf_options...]

Arguments:<URL_with_FUZZ>: The target URL. It must contain the keyword FUZZ exactly where you want ffuf to insert payloads from the wordlist (e.g., in the path, a parameter value, a subdomain).<full_wordlist_path>: The exact, full path to the wordlist file you want to use. The script does not automatically search for wordlists by filename.[ffuf_options...]: Any additional command-line options that are valid for the standard ffuf tool (e.g., -fc 404, -t 50, -H 'Header: Value', -recursion, -o output.json, etc.). These are passed directly to ffuf.Getting Help:For detailed help, examples, and wordlist suggestions: mifu -h or mifu --helpFor basic errors, a short usage message will be displayed.Examples1. Directory/Path Fuzzing:mifu [http://example.com/FUZZ](http://example.com/FUZZ) /usr/share/wordlists/dirb/common.txt -fc 404,403 -t 50
2. POST Parameter Fuzzing (Password):(Note: -X POST is added automatically by mifu because -d is used)mifu [http://example.com/login](http://example.com/login) /usr/share/wordlists/rockyou.txt -d 'user=admin&pass=FUZZ' -mc 200,302
3. Header Value Fuzzing:mifu [http://example.com/admin](http://example.com/admin) /usr/share/seclists/Fuzzing/LFI/LFI-common-files.txt -H 'X-Custom-IP: FUZZ' -fs 0
4. Fuzzing HTTP Methods:mifu [http://example.com/api/users](http://example.com/api/users) /path/to/methods.txt -X FUZZ -mc 200,405,403
Wordlistsmifu requires you to provide the full path to your chosen wordlist file.For suggestions on which wordlists might be suitable for different types of fuzzing (directories, passwords, LFI, etc.) and their common locations (often within /usr/share/wordlists/ or /usr/share/seclists/ if installed), please refer to the detailed help menu:mifu -h
