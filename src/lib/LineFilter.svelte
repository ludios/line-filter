<script lang="ts">
    export let lines: string[] = [];
    let lowercase_lines: string[] = lines.map(line => line.toLowerCase());

    let filter_string: string = "";
    let match_case: boolean = false;

    function include_line(line: string) {
        if (match_case) {
            return line.includes(filter_string);
        }
        return lowercase_lines.include(filter_string.toLowerCase());
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
    }
</style>

<input type="text" placeholder="Filter" bind:value={filter_string} />
<label>
    <input type="checkbox" bind:checked={match_case}>
    Match case
</label>

<table>
    <tbody>
        {#each lines as line}
            {#if line.includes(filter_string)}
                <tr>
                    <td class="line">{line}</td>
                </tr>
            {/if}
        {/each}
    </tbody>
</table>
