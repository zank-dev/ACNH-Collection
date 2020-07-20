<script>
    import { onMount, createEventDispatcher } from 'svelte';
    import Image from './Image.svelte';

    const dispatch = createEventDispatcher();
    const monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dez'];
    export let collection, colourMode;
    let progressBars = [];
    let date = new Date();

    function isActive(month, time) {
		if(month[date.getMonth()] === true){
			for (let i = 0; i < time.length; i++) {
				if(i % 2 === 0) { // to compare time[0] and time[1]
					if(date.getHours() >= time[i] && date.getHours() < time[i + 1]) {
						return true;
					}
				}
			}
		} else {
			return false;
		}
	}

	function calcTimeValue(time) {
		for(let i = 0; i < time.length; i++){
			if(date.getHours() >= time[i] && date.getHours() < time[i + 1]) {
				return (date.getHours() * 60 + date.getMinutes()) - (time[i] * 60);
			}
		}
	}

	function calcMaxTimeValue(time) {
		for(let i = 0; i < time.length; i++){
			if(date.getHours() >= time[i] && date.getHours() < time[i + 1]) {
				return (time[i + 1] * 60) - (time[i] * 60);
			}
		}
    }
    
    function setProgressBars() {
        let placeholerProgressBar = [];

        collection.forEach(element => {
            placeholerProgressBar.push(document.getElementById(element.name.toLowerCase().split(' ').join('_')));
        });

        return placeholerProgressBar;
    }

    function updateProgressBarTimes(){
        collection.forEach(element => {
            const current = calcTimeValue(element.time);
            const max = calcMaxTimeValue(element.time);

            const progressIndex = progressBars.find(bar => element.name.toLowerCase().split(' ').join('_') == bar.id);
            if(current || max !== undefined) {
                progressIndex.value = current;
                progressIndex.max = max;
                progressIndex.classList.remove('progress-last-hour');
                if(max - current <= 60){
                    progressIndex.classList.add('progress-last-hour');
                }
            } else {
                progressIndex.max = 100;
                progressIndex.classList.remove('progress-colour');
            }
        });
    }

	function setTime(time){
		let text = '';
		for(let i = 0; i < time.length; i++){
			if(i % 2 === 0){
				text += `${time[i]} to ${time[i + 1]} | `;
			}
		}
		return text.substring(0, text.length - 2);
    }
    
    function setMonth(month){
        let text = '';
        let startMonth = '';

        for(let i = 0; i < month.length; i++){
            if(month[i] === true && startMonth === '') {
                startMonth = monthNames[i];
            } 
            if(month[i] === false && startMonth !== '') {
                if(startMonth === monthNames[i - 1]){
                    text += monthNames[i - 1] + ' | ';
                } else {
                    text += startMonth + ' to ' + monthNames[i - 1] + ' | ';
                }
                startMonth = '';
            }
        }

        if(startMonth !== ''){
            if(startMonth === monthNames[month.length - 1]){
                    text += monthNames[month.length - 1] + ', ';
                } else {
                    text += startMonth + ' to ' + monthNames[month.length - 1] + ' | ';
                }
        }
        return text.substring(0, text.length - 2);
    }

    function playerCollection(id){
        dispatch('clickTable', id);
    }

    function getImage(name, type){
        const fileName = name.toLowerCase().split(' ').join('_');
        return './img/' + type + '/' + fileName + '.png';
    }

    function setID(name){
        return name.toLowerCase().split(' ').join('_');
    }

    onMount(() => {
        const interval = setInterval(() => {
            progressBars = setProgressBars();
            date = new Date();
            updateProgressBarTimes();
		}, 1000);

		return () => {
			clearInterval(interval);
		};
	});
</script>

{#each collection as { id, name, type, location, extra, time, month, price, active, caught, icon }}
    <tr class="c-hand" on:click={() => playerCollection(id)} class:active={ isActive(month, time) === true }>
    <td>
        <div class="popover popover-right">
            <figure class="avatar">
                <Image src={getImage(name, type)} alt={name} placeholder='./img/placeholder.png' />
                <img src="./img/icons/{type}_icon.svg" class="avatar-icon" alt={type}}>
            </figure>
            <div class="popover-container">
                <div class="card">
                    <div class="card-header">
                        <div class="float-right">
                            <figure class="avatar avatar-xl">
                                <Image src={getImage(name, type)} alt={name} placeholder='./img/placeholder.png' />
                            </figure>
                        </div>
                        <div class="card-title h5 text-dark">{name}</div>
                        <div class="card-subtitle text-gray">{location}{#if extra !== ''}<br>({extra}){/if}</div>
                    </div>
                    <div class="card-body text-dark">
                        <div class="float-left">
                             {setTime(time)}<br>
                             {setMonth(month)}
                        </div>
                        <div class="float-right">
                            <img src="./img/icons/{type}_icon.svg" class="avatar-icon" class:light-mode={ colourMode === 'light' } alt={type}}>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </td>
    <td>{name}{#if caught}<i class="icon icon-check float-right"></i>{/if}</td>
    {#if extra !== ''}
        <td class="tooltip" data-tooltip={extra}><u>{location}</u></td>
    {:else}
        <td>{location}</td>
    {/if}
    <td>
        <div class="popover popover-top">
            {#if calcTimeValue(time) >= 0}
                <progress class="progress" id={setID(name)} class:progress-colour={active === false}></progress>
            {:else}
                <progress class="progress" id={setID(name)}></progress>
            {/if}
            <div class="popover-container">
                <div class="card">
                    <div class="card-header">
                        <div class="card-title h5 text-dark">Time</div>
                        <div class="card-subtitle text-gray">{name}</div>
                    </div>
                    <div class="card-body text-dark">
                        <div >{setTime(time)}<i class="float-right icon icon-2x icon-time" /></div>
                    </div>
                </div>
            </div>
        </div>
    </td>
    <td class="hide-xs">
        {setMonth(month)}
    </td>
    <td class="hide-xs">{price}</td>
    </tr>
{/each}

<style>
	u {
		text-decoration-line: underline;
		text-decoration-style: wavy;
    }
      
  	.tooltip::after {
		color: #FFF;
 	}

  	figure.avatar {
		top: 0.25rem;
  	}
	
	.popover {
		align-items: center;
		  justify-content: center;
		  display: flex;
	}

    .progress {
		display: flex;
		margin: 0.75rem auto;
	}

	.progress-colour::-webkit-progress-value, .progress-colour::-moz-progress-bar {
		background: #FF2F4B !important;
	}

	.avatar-icon {
		filter: invert(100%);
		background: #FFF;
	}

    .light-mode {
        filter: invert(0%);
        background: rgba(0, 0, 0, 0);
    }
</style>