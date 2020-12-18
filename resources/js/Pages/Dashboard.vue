<template>
    <app-layout>
        <div class="py-12">
            <div class="max-w-7xl mx-auto">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                    <Card name="Discord"></Card>
                </div>
            </div>
        </div>
    </app-layout>
</template>

<script>
    import AppLayout from '@/Layouts/AppLayout'
    import Card from '@/Components/Card'

    export default {
        components: {
            AppLayout,
            Card
        },
    }
</script>
