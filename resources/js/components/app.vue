<template>
    <div v-html="message"></div>
</template>
<script>
import axios from "axios";

const default_layout = "default";

export default {
    computed: {},
    data() {
        return {
            message:
                "The ONE drawback and suggestion I have is a home button or gesture somewhere in the app! When I am deep into the app it gets really cumbersome to get back to the main screen. Also the battery starts to drain quite quick, but other than that I LOVE your app!"
        };
    },
    methods: {
        async getAnalysis() {
            const options = {
                method: "GET",
                url:
                    "https://twinword-sentiment-analysis.p.rapidapi.com/analyze/",
                params: {
                    text: this.message
                },
                headers: {
                    "X-RapidAPI-Host": process.env.MIX_RAPID_API_HOST,
                    "X-RapidAPI-Key": process.env.MIX_RAPID_API_KEY
                }
            };

            const { data } = await axios.request(options);

            let strArr = this.message.split(/(\s+)/);

            console.log(data);

            let fullText = this.message;
            const keywords = data.keywords;
            keywords.map(item => {
                const index = strArr.findIndex(str => {
                    return str.toLowerCase() === item.word.toLowerCase();
                });

                if (index > 0) {
                    if (item.score > 0) {
                        fullText = fullText.replace(
                            new RegExp("\\b" + item.word + "\\b", "gi"),
                            "<span class='green-sen'>" +
                                strArr[index] +
                                "</span>"
                        );
                    } else {
                        fullText = fullText.replace(
                            new RegExp("\\b" + item.word + "\\b", "gi"),
                            "<span class='red-sen'>" + strArr[index] + "</span>"
                        );
                    }
                }
            });
            this.message = fullText;
        }
    },
    mounted() {
        this.getAnalysis();
    }
};
</script>
