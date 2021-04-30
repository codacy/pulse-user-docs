# Installing the Pulse CLI

Download the latest version of the Pulse CLI for your operating system and make sure that you're able to run the binary.

1.  Take note of the latest version of the CLI:

    ![Latest version](https://img.shields.io/github/v/release/codacy/pulse-event-cli?sort=semver)

1.  Run the command to download the correct binary for your operating system, changing `<VERSION>` to the latest version obtained on the previous step:

    === "Linux"
        On 32-bit operating systems:

        ```sh
        curl -fsSL -o pulse-event-cli https://artifacts.codacy.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_386/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

        On 64-bit operating systems:
    
        ```sh
        curl -fsSL -o pulse-event-cli https://artifacts.codacy.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_amd64/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

    === "macOS"
        ```sh
        curl -fsSL -o pulse-event-cli https://artifacts.codacy.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_darwin_amd64/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

    === "Windows"
        On 32-bit operating systems:

        ```
        curl -fsSL -o pulse-event-cli.exe https://artifacts.codacy.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_386/pulse-event-cli.exe
        ```
    
        On 64-bit operating systems:

        ```
        curl -fsSL -o pulse-event-cli.exe https://artifacts.codacy.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_amd64/pulse-event-cli.exe
        ```

1.  Test if you can run the CLI:

    === "Linux and macOS"
        ```sh
        ./pulse-event-cli help
        ```

    === "Windows"
        ```sh
        pulse-event-cli.exe help
        ```

After installing the CLI you're ready to [start pushing data to Pulse](pushing-data-to-pulse.md).
