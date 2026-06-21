<script>
    import { page } from "$app/stores";
    import Icon from "$lib/components/Icon.svelte";
    import {
        loadSettings,
        saveSettings,
        userSettings,
    } from "$lib/userSettings.js";
    import { onMount } from "svelte";
    import Dialog from "$lib/components/Dialog.svelte";

    /** @typedef {import("$lib/types.js").Theme} Theme */

    /** @type {Theme[]} */
    const themes = ["light", "dark", "amoled", "catppuccin"];

    onMount(() => {
        loadSettings();
    });

    const changeTheme = (
        /** @type {MouseEvent} */ e,
        /** @type {Theme} */ theme,
    ) => {
        e.preventDefault();

        $userSettings.theme = theme;
        saveSettings();
    };
</script>

<svelte:head>
    <title
        >{$page.url.hostname}{(() => {
            const path = $page.url.pathname.slice(1);
            return path ? ` / ${path}` : "";
        })()}</title
    >
    <link
        rel="stylesheet"
        href="/static/styles/{$userSettings.theme || 'dark'}.css"
    />
</svelte:head>

<main>
    <div class="wrapper">
        <header class="header">
            <div class="header-content">
                <h1 class="brand">{$page.url.hostname}</h1>
                <p class="tagline">Fast, private file sharing</p>
            </div>
        </header>

        <nav class="navigation">
            <div class="nav-left">
                <ul>
                    <li>
                        <a
                            aria-current={$page.url.pathname === "/"
                                ? "page"
                                : undefined}
                            href="/">Home</a
                        >
                    </li>
                    <li>
                        <a
                            aria-current={$page.url.pathname === "/uploaders"
                                ? "page"
                                : undefined}
                            href="/uploaders">Uploaders</a
                        >
                    </li>
                </ul>
            </div>
            <div class="nav-right">
                <ul class="nav-links">
                    <li>
                        <a href="https://agu.lol" target="_blank" class="nav-link">Agu.LoL</a>
                    </li>
                    <li>
                        <a
                            href="https://github.com/TrinityTF"
                            target="_blank"
                            class="nav-link">GitHub</a
                        >
                    </li>
                </ul>
            </div>
        </nav>

        <noscript>JavaScript is required for this website.</noscript>

        <details class="settings">
            <summary>⚙️ Settings</summary>
            <div class="container">
                <div class="option themes">
                    <span>Theme:</span>
                    {#each themes as theme}
                        <a
                            href="/"
                            class="theme-name"
                            on:click={(e) => changeTheme(e, theme)}>{theme}</a
                        >
                    {/each}
                </div>
                <label class="option">
                    <input
                        type="checkbox"
                        id="file-ext"
                        name="file-ext"
                        bind:checked={$userSettings.appendFileExt}
                        on:change={saveSettings}
                    />
                    Append file extension to URL
                </label>
                <label class="option">
                    <input
                        type="checkbox"
                        id="file-history"
                        name="file-history"
                        bind:checked={$userSettings.rememberFileHistory}
                        on:change={saveSettings}
                    />
                    Remember upload history, stored locally
                </label>
                <label class="option">
                    <input
                        type="checkbox"
                        id="file-disposition"
                        name="file-disposition"
                        bind:checked={$userSettings.fileContentDisposition}
                        on:change={saveSettings}
                    />
                    Share your file's original name&nbsp;
                    <span
                        class="tooltip"
                        title="Enabling this will show your file's original (local) name to other users in supported browsers; e.g. when saving."
                        >( ? )</span
                    >
                </label>
                <label class="option">
                    <input
                        type="checkbox"
                        id="file-stripexif"
                        name="file-stripexif"
                        bind:checked={$userSettings.stripExif}
                        on:change={saveSettings}
                    />
                    Entirely strip Exif data from image
                </label>
                <label class="option">
                    <input
                        type="checkbox"
                        id="file-showthumb"
                        name="file-showthumb"
                        bind:checked={$userSettings.showThumbnails}
                        on:change={saveSettings}
                    />
                    Show file thumbnails
                </label>
            </div>
        </details>

        <slot />
    </div>
</main>

<style lang="scss">
    :root {
        --font-body: "Noto Sans", sans-serif, -apple-system, "Helvetica Neue";
    }

    main {
        display: flex;
        justify-content: center;
        min-height: 100vh;
        margin: 0;
        background-color: rgb(var(--bg));
        font-family: var(--font-body);
        color: rgb(var(--fg));
    }

    h1 {
        font-weight: 400;
    }

    [aria-current="page"] {
        color: rgb(var(--primary));
        text-decoration: none;
        font-weight: 600;
    }

    input {
        font-size: inherit;
        font-family: inherit;
        margin-left: 0;
        accent-color: rgb(var(--primary));

        &:focus:not(:focus-visible) {
            outline: none;
        }
    }

    @media screen and (max-width: 800px) {
        .wrapper {
            width: 100%;
        }

        .header-content {
            text-align: center;
        }

        .navigation {
            flex-direction: column !important;
            gap: 1.5rem;
        }

        .nav-left,
        .nav-right {
            justify-content: center;
        }
    }

    .wrapper {
        width: 800px;
        padding: 2rem 1rem;
    }

    .header {
        margin-bottom: 3rem;
        padding-bottom: 2rem;
        border-bottom: 2px solid rgba(var(--primary), 0.3);
    }

    .header-content {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
    }

    .brand {
        margin: 0;
        font-size: 3rem;
        font-weight: 700;
        background: linear-gradient(135deg, rgb(var(--primary)), rgb(var(--primary)) 50%, rgb(var(--fg2)));
        background-clip: text;
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        letter-spacing: -0.5px;
    }

    .tagline {
        margin: 0;
        font-size: 1rem;
        color: rgb(var(--fg));
        opacity: 0.7;
    }

    .navigation {
        display: flex;
        align-items: center;
        gap: 2rem;
        margin-bottom: 2rem;
        padding-bottom: 1.5rem;
        border-bottom: 1px solid rgba(var(--outl2), 0.5);
    }

    .nav-left {
        display: flex;
    }

    .nav-right {
        display: flex;
        margin-left: auto;
    }

    ul {
        display: flex;
        flex-direction: row;
        padding: 0;
        margin: 0;
        list-style-type: none;
        gap: 0;
    }

    li {
        display: flex;
        align-items: center;
        
        &:not(:last-child)::after {
            content: "•";
            margin: 0 1rem;
            opacity: 0.3;
        }
    }

    .nav-link {
        color: rgb(var(--fg));
        font-weight: 500;
        transition: all 200ms ease;
        padding: 0.5rem 0;

        &:hover {
            color: rgb(var(--primary));
            text-decoration: none;
        }
    }

    .nav-links {
        gap: 0;
    }

    .tooltip {
        cursor: help;
        text-decoration: underline dotted;
    }

    .settings {
        display: flex;
        background-color: rgb(var(--bg_h));
        width: fit-content;
        border-radius: 0.5rem;
        gap: 2px;
        flex-direction: column;
        margin: 1.5rem 0;
        overflow: hidden;
        border: 1px solid rgba(var(--primary), 0.2);
        transition: all 200ms ease;

        &[open] {
            background-color: rgb(var(--bg2));
            summary {
                border-bottom: 2px solid rgb(var(--primary));
                background-color: rgba(var(--primary), 0.05);
            }
        }

        summary {
            cursor: pointer;
            padding: 0.75rem 1rem;
            font-weight: 500;
            user-select: none;
            transition: all 200ms ease;

            &:hover {
                background-color: rgba(var(--primary), 0.05);
            }
        }

        .container {
            display: flex;
            flex-direction: column;
            padding: 0.75rem 1rem;
            gap: 0.75rem;
        }
    }

    .option {
        display: flex;
        align-items: center;
        gap: 0.5rem;
        max-width: max-content;
    }

    .themes {
        gap: 1.5ch;
        margin-bottom: 0.5rem;
    }

    .theme-name {
        text-transform: capitalize;
        padding: 0.25rem 0.5rem;
        border-radius: 0.25rem;
        transition: all 150ms ease;

        &:hover {
            background-color: rgba(var(--primary), 0.1);
        }
    }
</style>
