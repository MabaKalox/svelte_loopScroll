<script>
    export let color;
    export let hour;
</script>

<div style={`background-color: ${color}`}>
    <div>{hour}</div>
</div>

<style type="text/scss">
    div {
        margin: 0;
        display: flex;
        justify-content: center;
        align-items: center;
    }
</style>