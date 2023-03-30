<template>
    <main>
        <form @submit.prevent="handleAnswer">
            <label for="question-input">Question:</label>
            <textarea
                id="question-input"
                name="question"
                v-model="question"
                rows="4"
                cols="50"
                required
            ></textarea>
            <button type="submit">Generate Response</button>
        </form>
        <div>{{ answer }}</div>
    </main>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import axios, { AxiosResponse } from 'axios'

interface CompletionResponse {
    choices: { text: string }[]
}

const question = ref('')
const answer = ref('')

async function handleAnswer(): Promise<void> {
    const url: string =
        'https://api.openai.com/v1/engines/davinci-codex/completions'
    const apiKey: string = '<YOUR_API_KEY_HERE>'

    try {
        const response: AxiosResponse<CompletionResponse> = await axios.post(
            url,
            {
                prompt: question.value,
                max_tokens: 50,
                n: 1,
                stop: ['\n']
            },
            {
                headers: {
                    'Content-Type': 'application/json',
                    Authorization: `Bearer ${apiKey}`
                }
            }
        )

        const data: CompletionResponse = response.data
        answer.value = data.choices[0].text
    } catch (error) {
        console.error(error)
    }
}
</script>
