<template>
    <h4>Статистика пользователя {{ $props.userId }}</h4>
    <votes-list :votes="votes" />
</template>

<script lang="ts">
import { Options, mixins } from "vue-class-component";
import StoreMixin from "../../mixins/store.mixin";
import QueryMixin from "../../mixins/query.mixin";
import type { Vote } from "../../types/types";

type PropsType = {
    userId: number;
};

@Options({
    props: {
        userId: { type: Number, required: true },
    },
})
export default class StatisticPageComponent extends mixins(
    StoreMixin,
    QueryMixin
) {
    declare $props: PropsType;

    async created() {
        const votes: Vote[] = await this.getVotes(this.$props.userId);
        this.setVotes(votes);
        await this.getFilms(votes);
    }

    async getFilms(votes: Vote[]) {
        const timeout = 100;

        for (var i = 0; i < votes.length; i++) {
            const vote: Vote = votes[i];

            const filmData = await this.getObjectQuery(vote.type, vote.filmId);
            if (filmData) {
                this.addFilm(filmData);
            }

            await new Promise((resolve) => setTimeout(resolve, timeout));
        }
    }
}
</script>
