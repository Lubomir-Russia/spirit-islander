<script lang="ts">
	import { createEventDispatcher } from "svelte";
	import Button from "@components/shared/Button.svelte";
	import Card from "@components/shared/Card.svelte";
	import CardGroup from "@components/shared/CardGroup.svelte";
	import DifficultyEmblem from "@components/shared/DifficultyEmblem.svelte";
	import ExpansionEmblem from "@components/shared/ExpansionEmblem.svelte";
	import Page from "@components/shared/Page.svelte";
	import { getAdversaryById } from "@functions/get-adversary-by-id";
	import { getDifficulty } from "@functions/get-difficulty";
	import { pluralize } from "@functions/utility/pluralize";
	import type { IAdversaryLevel } from "@models/game/adversaries";
	import type { Players } from "@models/game/players";
	import type { Difficulty } from "@models/game/difficulty";
	import type { IMap } from "@models/game/maps";
	import type { ExpansionName } from "@models/game/expansions";
	import type { IScenario } from "@models/game/scenarios";
	import type { ISpirit } from "@models/game/spirits";

	const dispatcher = createEventDispatcher<{
		reset: void;
		generate: void;
	}>();
	export let players: Players;
	export let difficulty: Difficulty;
	export let spirits: ISpirit[];
	export let expansions: ExpansionName[];
	export let map: IMap;
	export let adversary: IAdversaryLevel;
	export let scenario: IScenario;

	$: adversaryName = getAdversaryById(adversary.id);
</script>

<Page>
	<div class="game-setup page-content">
		<CardGroup name="Setup"
			description="Details of your generated game setup"
		>
			<Card name="players">
				<h3 class="card-header">
					Players
				</h3>
				<p class="content datum">
					{players}
				</p>
			</Card>
			<Card name="difficulty">
				<h3 class="card-header">
					Difficulty
				</h3>
				<p class="content datum">
					{difficulty}
				</p>
			</Card>
			<Card name="expansions">
				<h3 class="card-header">
					{pluralize(expansions.length, "Expansion")}
				</h3>
				<ul class="content">
					{#if expansions.length}
						{#each expansions as expansion}
							<li class="datum">
								{expansion}
							</li>
						{/each}
					{:else}
						<li>No Expansions</li>
					{/if}
				</ul>
			</Card>
			<Card name="spirits">
				<h3 class="card-header">
					{pluralize(players, "Spirit")}
				</h3>
				<ul class="content">
					{#each spirits as spirit}
						<li class="datum">
							{spirit.name}
							<ExpansionEmblem value={spirit.expansion} />
						</li>
					{/each}
				</ul>
			</Card>
			
			<Card name="map">
				<h3 class="card-header">
					Map
				</h3>
				<p class="content datum">
					{map.name} <DifficultyEmblem value={getDifficulty(map.difficulty, expansions)} />
				</p>
			</Card>
			
			<Card name="scenario">
				<h3 class="card-header">
					Scenario
				</h3>
				<p class="content datum">
					{scenario.name}
					<DifficultyEmblem value={getDifficulty(scenario.difficulty, expansions)} />
					<ExpansionEmblem value={scenario.expansion} />
				</p>
			</Card>
			
			<Card name="adversary">
				<h3 class="card-header">
					Adversary
				</h3>
				<p class="content datum">
					{adversaryName}
					{#if adversaryName !== "No Adversary"}
						Level {adversary.level}
						<DifficultyEmblem value={getDifficulty(adversary.difficulty, expansions)} />
					{/if}
				</p>
			</Card>
		</CardGroup>
	</div>

	<div class="page-buttons">
		<Button on:clicked={() => dispatcher("reset")}>
			Edit
		</Button>
		<Button on:clicked={() => dispatcher("generate")}>
			Regenerate
		</Button>
	</div>
</Page>

<style lang="scss">
	.game-setup  {

		:global(.card-group.setup) {
			grid-template-areas:
				"players players difficulty difficulty"
				"expansions expansions expansions expansions"
				"spirits spirits spirits spirits"
				"map map map map"
				"scenario scenario scenario scenario"
				"adversary adversary adversary adversary";

			@media screen and (min-width: 768px) {
				grid-template-areas:
					"players difficulty expansions expansions"
					"spirits spirits map map"
					"scenario scenario adversary adversary";
			}
		}
	}

	.card-header {
		margin-bottom: 0;
	}

	.content {
		padding: 16px 8px;

		@media screen and (min-width: 768px) {
			padding: 16px;
		}
	}

	:global(.expansions) {
		.content {
			display: flex;
			flex-wrap: wrap;
		}

		.datum {
			flex: 0 0 50%;
		}
		
	} 

	.datum {
		display: flex;
		align-items: center;
	}
</style>
