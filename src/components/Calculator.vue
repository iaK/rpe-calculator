<template>
    <div class="container mx-auto px-4 flex items-center justify-center">
        <div class="bg-white rounded-lg px-4 py-6 mb-8 max-w-md">
            <h2 class="mb-4 text-gray-700 font-normal text-3xl">Räkna ut RPE</h2>
            <div class="mb-4">
                Jag har lyft <input type="text" style="width: 60px" class="bg-gray-100 text-center p-2 my-2" v-model="doneWeight"> kg,
                <input type="text" style="width: 35px" class="bg-gray-100 text-center p-2 my-2 ml-2" v-model="doneReps"> gånger, på
                RPE <input type="text" style="width: 40px" class="bg-gray-100 text-center p-2 my-2" v-model="doneRPE">.
            </div>

            <div class="mb-4">
                Jag vill lyfta <input type="text" style="width: 35px" class="bg-gray-100 text-center p-2 my-2" v-model="wantedReps"> gånger på
                RPE <input type="text" style="width: 40px" class="bg-gray-100 text-center p-2 my-2" v-model="wantedRPE">
            </div>
            <button @click="calculate" class="bg-blue-500 text-white font-bold py-2 px-4 border-b-4 hover:border-b-2 hover:border-t-2 border-blue-300 hover:border-blue-300 rounded mb-4">
                Beräkna!
            </button>

            <p class="text-red px-4 leading-normal" v-if="error">
                {{ error }}
            </p>
            <p v-if="weight && !error" class="flex items-center text-center justify-center w-full">
                Du kan lyfta <span class="text-3xl mx-2">{{ weight }}</span> kg
            </p>

        </div>
    </div>
</template>

<script>

    import table from '../table.js';

    export default {

        data() {
            return {
                table: table,
                wantedWeight: 0,
                wantedReps: 0,
                wantedRPE: 0,
                doneWeight: 0,
                doneReps: 0,
                doneRPE: 0,
                weight: 0,
                error: null,
            }
        },

        methods: {
            calculate() {
                this.error = null;

                let max, weight;

                let done = this.findRPE(this.doneRPE, this.doneReps);
                let wanted = this.findRPE(this.wantedRPE, this.wantedReps);

                if (done && wanted && done.length && wanted.length) {
                    max = (this.doneWeight / done[0].percentage) * 100;
                    weight = Math.floor(((max / 100) * wanted[0].percentage) * 10) / 10;
                }

                if (! weight || isNaN(weight)) {
                    this.error = "Något är knas. RPE-värden 6.5 till 10, Reps från 1 till 10, använd punkt som decimal";
                } else {
                    this.weight = weight;
                }
            },

            findRPE(searchRPE, searchReps) {
                let row;

                this.table.filter((rpe) => {
                    if (rpe.rpe == searchRPE) {
                        row = rpe.range.filter((set) => {
                            return set.reps == searchReps;
                        });
                    }
                });

                return row
            }

        }

    }

</script>
