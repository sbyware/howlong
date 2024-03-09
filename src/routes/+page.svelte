<script lang="ts">
    import { Calendar as CalendarIcon, Clock } from "lucide-svelte";
    import { type DateValue, DateFormatter, getLocalTimeZone } from "@internationalized/date";
    import { cn, getTimeUnitsMap } from "$lib/utils.js";
    import { Button } from "$lib/components/ui/button";
    import { Calendar } from "$lib/components/ui/calendar";
    import * as Popover from "$lib/components/ui/popover";

    const df = new DateFormatter("en-US", {
        dateStyle: "long",
        timeStyle: "short"
    });

    let value: DateValue | undefined = undefined;
    let now = new Date();
    $: inFuture = value && value.toDate(getLocalTimeZone()) > new Date();

    setInterval(() => {
        now = new Date();
    }, 1000);
</script>

<div class="flex h-full w-full flex-col items-center justify-center gap-3">
    <div class="m-auto flex w-96 flex-col gap-4">
        <div class="flex w-full flex-col items-center gap-1">
            <Clock class="size-20 " strokeWidth={1} />
            <span class="text-2xl font-medium">
                how long
                {#if value}
                    {inFuture ? "until" : "since"}...
                {/if}
            </span>
            <span class="text-xs text-muted-foreground"
                >it is currently {new DateFormatter("en-US", {
                    dateStyle: "long",
                    timeStyle: "long"
                }).format(now)}</span>
        </div>
        <Popover.Root openFocus>
            <Popover.Trigger asChild let:builder>
                <Button
                    variant="outline"
                    class={cn(
                        "flex-none justify-start gap-2 px-3 text-left font-normal shadow-md ring-1 ring-black/10",
                        !value && "text-muted-foreground"
                    )}
                    builders={[builder]}>
                    <CalendarIcon class="aspect-square h-4 w-4 opacity-50" />
                    {value ? df.format(value.toDate(getLocalTimeZone())) : "select a date"}
                </Button>
            </Popover.Trigger>
            <Popover.Content class="w-auto p-0">
                <Calendar bind:value initialFocus />
            </Popover.Content>
        </Popover.Root>

        {#if value}
            {@const units = getTimeUnitsMap(value.toDate(getLocalTimeZone()) - now)}
            <div class="flex w-full flex-wrap gap-2 rounded-lg">
                {#each units as [unit, value]}
                    <div
                        class="flex flex-grow flex-col items-center rounded-lg border border-black/20 bg-white p-4 shadow-md">
                        <div class="font-mono text-2xl font-bold">{value}</div>
                        <div class="text-xs text-black/50">
                            {unit}{value > 1 ? "s" : ""}
                        </div>
                    </div>
                {/each}
            </div>
        {/if}
    </div>
</div>
