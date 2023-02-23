<div>
    {#await generateQuestion()}
        ...
    {:then question} 
        { question }

        {#await generateAnswers(question)}
            ...
        {:then answers} 
            {#each answers as answer}
            <div>
                { answer }
            </div>
            {/each}
        {/await}
    {/await}
</div>

<style lang="scss">

</style>

<script lang="ts">
    type Operator = '+' | '-' | '*' | '/';
    type Range = { from: number, to: number };

    export const range: Range = {
        from: 0,
        to: 50,
    }

    export const possibleAnswers = 5;

    function scrambleArray(arr: any[]): any[] {
        let scrambledArray: any[] = [];

        while(arr.length != 0) {
            scrambledArray.push(arr.splice(Math.floor(Math.random() * arr.length), 1))
        }

        return scrambledArray;
    }

    async function generateAnswers(question: any): Promise<number[]> {
        let answers: number[] = [];
        answers.push(eval(question));

        for (let i = 0; i < possibleAnswers - 1; i++) {
            let question: string;

            do {
                question = await generateQuestion();
            } while (answers.includes(eval(question)));

            answers.push(eval(question));
        }

        return scrambleArray(answers);
    }

    function randomOperator() {
        const ops: Operator[] = ['+', '-' /*, '*'*/, '/'];
        return ops[Math.floor(Math.random() * ops.length)];
    }

    function randomNum(range: Range): number {
        return Math.floor(Math.random() * range.to) + range.from;
    }

    async function generateQuestion(): Promise<string> {
        const op = randomOperator();
        let q: string;

        do {
            q = `${randomNum(range)} ${op} ${randomNum(range)}`;
        } while (eval(q) % 1 !== 0); 

        return q;
    }
</script>
