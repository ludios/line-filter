<script lang="ts">
    import escapeRegExp from 'lodash.escaperegexp';

    export let lines:     string[] = [];

    let filter_string:    string = "";
    let match_case:       boolean = false;
    let match_whole_word: boolean = false;
    let use_regexp:       boolean = false;

    $: maybe_wrap_with_b = function(s: string): string {
        return match_whole_word ? `\\b${s}\\b` : s;
    }
    $: escaped_filter_string =
        maybe_wrap_with_b(
            use_regexp ?
                filter_string
                : escapeRegExp(filter_string)
        );
    $: regexp = new RegExp(escaped_filter_string, match_case ? undefined : "i");
    $: {
        console.log(
            `filter_string = ${JSON.stringify(filter_string)},`,
            `match_case = ${match_case},`,
            `regexp = ${regexp}`
        );
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
    input[type=checkbox] {
        transform: scale(1.25);
        margin: 0px 4px 0 4px;
    }
    label {
        user-select: none;
    }
</style>

<input size=38 type="text" placeholder="Filter" bind:value={filter_string} />
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

<table>
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
