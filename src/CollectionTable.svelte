<script>
	import data from './acnh_collection.json';
	import { onMount } from 'svelte';
	import Collection from './Collection.svelte';

	const bugs = data.bugs;
	const fishs = data.fishs;
	const sea = data.deepSea;

	export let search, colourMode, caughtMode;
	let storedCollection = localStorage.getItem('collection') ? JSON.parse(localStorage.getItem('collection')) : [];
	let filteredCollection, catchedCollection, notCatchedCollection = [];
	let date = new Date();
	$:bugNumber = new Set();
	$:fishNumber = new Set();
	$:underwaterNumber = new Set();

	bugs.map((value) => {
		{value.type = 'bug';}
	});

	fishs.map((value) => {
		{value.type = 'fish';}
	});
	
	sea.map((value) => {
		{value.type = 'underwater';}
	});
	
	let collection = [...bugs, ...fishs, ...sea];

	collection.map((value, index) => {
	  {value.id = index;}
	  {value.priority = 0}
	  {value.active = false}
	  {value.caught = storedCollection.includes(index)}
	});

	function setPriority(array) {
		let prio = 0;

		for(let i = 0; i < array.month.length; i++){
			if(array.month[date.getMonth()] === true && i >= date.getMonth()) {
				for (let j = 0; j < array.time.length; j += 2) {
					if(date.getHours() === array.time[j + 1] - 1) { 
						if(array.time.length > 4){ // if the array is long
							prio -= date.getMinutes() / 2;
						} else {
							prio -= date.getMinutes();
						}
					}
					if(date.getHours() >= array.time[j] && date.getHours() < array.time[j + 1]) {
						if(array.time[j + 1] === 24 && array.time.length > 2) { // when there is a new day inbetween
							prio -= 25 - (array.time[j] - array.time[j - 1]);
						} else {
							prio -= 25 + array.time[j] - array.time[j + 1];
							if(array.time.length > 4) {
								prio--;
							}
						}
						array.active = true;
					} else {
						if(array.time.length > 4) {
							prio++;
						} else if(array.time[j + 1] === 24 && array.time.length > 2) {
							prio += 24 - (array.time[j] - array.time[j - 1]);
						} else {
							prio += array.time[j + 1] - array.time[j];
						}
					}
				}
			} else if (array.month[date.getMonth()] === false && i <= date.getMonth()) {
				if(array.month[i] === false) { // if false half of days up otherwise full monthdays
					prio += new Date (date.getFullYear(), i, 0).getDate() / 2;
				} else {
					prio += new Date (date.getFullYear(), i, 0).getDate();
				}
			}
		}
		return prio;
	}

	function clickTable(event){
		if(storedCollection.includes(event.detail)) {
			const index = storedCollection.indexOf(event.detail);
			let type;
			storedCollection.splice(index, 1);
			collection.find(element => {
					type = element.type;
					return element.id == event.detail;
				}).caught = false;
			
			if(type === 'bug'){
				bugNumber.delete(event.detail);
			}

			if(type  === 'fish'){
				fishNumber.delete(event.detail);
			}

			if(type  === 'underwater'){
				underwaterNumber.delete(event.detail);
			}
        } else {
			storedCollection.push(event.detail);
			collection.find(element => element.id == event.detail).caught = true;
			collection = collection;
		}
		collection = collection
		localStorage.setItem('collection', JSON.stringify(storedCollection));
	}

	$: {
		collection.forEach(element => {
			element.active = false;
			element.priority = setPriority(element);
		});
		
		collection.sort((a, b) => {
			return a.priority - b.priority;
		});

		storedCollection.forEach(index => {
			if(collection.find(element => element.id == index).type === 'bug'){
				bugNumber.add(index);
			}

			if(collection.find(element => element.id == index).type  === 'fish'){
				fishNumber.add(index);
			}

			if(collection.find(element => element.id == index).type  === 'underwater'){
				underwaterNumber.add(index);
			}
		});

		if(caughtMode === 'uncaught'){
			catchedCollection = collection.filter(element => {
				return !storedCollection.includes(element.id);
			});

			notCatchedCollection = collection.filter(element => {
				return storedCollection.includes(element.id);
			});
		} else {
			notCatchedCollection = collection.filter(element => {
				return !storedCollection.includes(element.id);
			});

			catchedCollection = collection.filter(element => {
				return storedCollection.includes(element.id);
			});
		}
		collection = collection;
	}

	$: filteredCollection = [...catchedCollection.filter(element => {
			const regex = new RegExp(search, "i");
			return element.name.match(regex) || element.type.match(regex) || element.location.match(regex);
		}), ...notCatchedCollection.filter(element => {
			if(search !== '' && !'bug' && !'fish' && !'underwater') {
				const regex = new RegExp(search, "i");
				return element.name.match(regex) || element.type.match(regex) || element.location.match(regex);
			} else {
				return null;
			}
		})];
	
	$: notFilteredCollection = [...catchedCollection.filter(element => {
			const regex = new RegExp(search, "i");
			return !element.name.match(regex) && !element.type.match(regex) && !element.location.match(regex);
		})]


	onMount(() => {
		const interval = setInterval(() => {
			date = new Date();
			collection = collection;
		}, 1000);

		return () => {
			clearInterval(interval);
		};
	});
</script>

<div>
	<span class="label label-primary" class:label-success={ bugNumber.size === bugs.length}>
		<img src="./img/icons/bug_icon.svg" alt="bug">Bug: {bugNumber.size} / {bugs.length}
	</span>
	<span class="label label-primary" class:label-success={ fishNumber.size === fishs.length}>
		<img src="./img/icons/fish_icon.svg" alt="fish">Fish: {fishNumber.size} / {fishs.length}
	</span>
	<span class="label label-primary" class:label-success={ underwaterNumber.size === sea.length}>
		<img src="./img/icons/underwater_icon.svg" alt="underwater">Deep-sea: {underwaterNumber.size} / {sea.length}
	</span>
</div>
<table class="table table-hover">
	<thead>
		<tr>
			<th />
			<th>Name</th>
			<th>Location / Movement</th>
			<th>Time</th>
			<th class="hide-xs">Month</th>
			<th class="hide-xs">Price</th>
		</tr>
	</thead>
	<tbody>
		{#if filteredCollection.length > 0}
			<Collection {colourMode} collection={filteredCollection} on:clickTable={clickTable} />
		{:else}
			<td class="table-devider" colspan="8">Nothing found!</td>
		{/if}
		{#if notFilteredCollection.length > 0}
			<td class="table-devider" colspan="8">&nbsp;</td>
			<Collection {colourMode} collection={notFilteredCollection} on:clickTable={clickTable} />
		{/if}
	</tbody>
</table>

<style>
  	.table.table-hover tbody tr:hover {
		color: #5755D9;
 	}

	td.table-devider{
		font-weight: bold;
	}

	span.label {
		display: inline-block;
		vertical-align: middle;
		margin-right: 0.5rem;
	}

	span img {
		display: inline-block;
		vertical-align: middle;
		width: auto;
		height: 0.8rem;
		margin: 0 0.25rem;
	}
</style>