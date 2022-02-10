<script lang="ts">
    import escapeRegExp from 'lodash.escaperegexp';

    export let lines:     string[] = [];

    let regexp:           RegExp;
    let valid_regexp:     boolean;
    let filter_string:    string = "";
    let match_case:       boolean = false;
    let match_whole_word: boolean = false;
    let use_regexp:       boolean = false;
    let table_element:    HTMLElement;
    let table_width:      number;

    $: maybe_wrap_with_b = function(source: string): string {
        return match_whole_word ? `\\b${source}\\b` : source;
    }
    $: regexp_source =
        maybe_wrap_with_b(
            use_regexp ?
                filter_string
                : escapeRegExp(filter_string)
        );
    $: {
        try {
            regexp = new RegExp(regexp_source, match_case ? undefined : "i");
            valid_regexp = true;
        } catch (e) {
            if (!(e instanceof SyntaxError)) {
                throw e;
            }
            console.log(`Invalid RegExp: ${regexp_source}, error = ${e}`);
            regexp = new RegExp(".^"); // don't match anything
            valid_regexp = false;
        }
        console.log(
            `filter_string = ${JSON.stringify(filter_string)},`,
            `match_case = ${match_case},`,
            `regexp = ${regexp}`,
            `valid_regexp = ${valid_regexp}`,
        );
        // Don't let the table become narrower than it was initially
        table_width = table_element?.offsetWidth;
    }
    $: include_line = function(line: string): boolean {
        return regexp.test(line);
    }
</script>

<style>
    :root {
        --border-color: #888;
    }
    table {
        border-collapse: collapse;
    }
    td.line {
        white-space: pre;
        border: 1px solid var(--border-color);
        padding: 0 4px;
    }
    input {
        border: 1px solid var(--border-color);
        margin: 4px 0;
        padding: 4px;
    }
    input[aria-invalid="true"] {
        background-color: darkred;
    }
    input[type=checkbox] {
        transform: scale(1.25);
        margin: 0px 4px 0 4px;
    }
    label {
        user-select: none;
    }
</style>

<input size=38 type="text" placeholder="Filter" bind:value={filter_string} aria-invalid={!valid_regexp} />
<label>
    <input type="checkbox" bind:checked={match_case}>
    Match case
</label>
<label>
    <input type="checkbox" bind:checked={match_whole_word}>
    Match whole word
</label>
<label>
    <input type="checkbox" bind:checked={use_regexp}>
    Use RegExp
</label>

<table bind:this={table_element} style:min-width="{table_width}px">
    <tbody>
        {#each lines as line}
            {#if include_line(line)}
                <tr>
                    <td class="line">{line}</td>
                </tr>
            {/if}
        {/each}
    </tbody>
</table>
