/*
 * Script Name: Single Village Snipe
 * Version: v1.2.4
 * Last Updated: 2021-05-01
 * Author: RedAlert
 * Author URL: https://twscripts.ga/
 * Author Contact: RedAlert#9859 (Discord)
 * Approved: N/A (approved after the script approval rules change)
 * Approved Date: 2021-02-27
 * Mod: JawJaw
 */

var scriptData = {
	name: 'Single Village Snipe',
	version: 'v1.2.4',
	author: 'RedAlert',
	authorUrl: 'https://twscripts.ga/',
	helpLink: 'https://forum.tribalwars.net/index.php?threads/single-village-snipe.286731/',
};

// User Input
if (typeof DEBUG !== 'boolean') DEBUG = false;

// Constants
var LS_PREFIX = 'raSingleVillageSnipe';
var TIME_INTERVAL = 60 * 60 * 1000 * 24 * 365; // unit info does not change during the whole world duration so we only need to get it once
var GROUP_ID = localStorage.getItem(`${LS_PREFIX}_chosen_group`) ?? 0;
var LAST_UPDATED_TIME = localStorage.getItem(`${LS_PREFIX}_last_updated`) ?? 0;

// Globals
var unitInfo,
	villages = [],
	troopCounts = [];

// Translations
var translations = {
	en_DK: {
		'Single Village Snipe': 'Single Village Snipe',
		Help: 'Help',
		'This script can only be run on a single village screen!':
			'This script can only be run on a single village screen!',
		'Landing Time': 'Landing Time',
		'Calculate Launch Times': 'Calculate Launch Times',
		'Export as BB Code': 'Export as BB Code',
		'Landing time was updated!': 'Landing time was updated!',
		'Plan for:': 'Plan for:',
		'Landing Time:': 'Landing Time:',
		Unit: 'Unit',
		From: 'From',
		'Launch Time': 'Launch Time',
		Command: 'Command',
		Status: 'Status',
		Send: 'Send',
		'Error fetching village groups!': 'Error fetching village groups!',
		'Choose Units to Snipe': 'Choose Units to Snipe',
		Group: 'Group',
		'No possible snipe options found!': 'No possible snipe options found!',
		Distance: 'Distance',
		'An error occured while fetching troop counts!': 'An error occured while fetching troop counts!',
		'snipe attempts found': 'snipe attempts found',
		'Nothing to export!': 'Nothing to export!',
		'Target:': 'Target:',
		'Send in': 'Send in',
		'Destination Village': 'Destination Village',
		Sigil: 'Sigil',
		'Min. Amount': 'Min. Amount',
		'Export Config': 'Export Config',
		'Import Config': 'Import Config',
		'Configuration imported successfully!': 'Configuration imported successfully!',
		'Nothing to import!': 'Nothing to import!',
	},
	en_US: {
		'Single Village Snipe': 'Single Village Snipe',
		Help: 'Help',
		'This script can only be run on a single village screen!':
			'This script can only be run on a single village screen!',
		'Landing Time': 'Landing Time',
		'Calculate Launch Times': 'Calculate Launch Times',
		'Export as BB Code': 'Export as BB Code',
		'Landing time was updated!': 'Landing time was updated!',
		'Plan for:': 'Plan for:',
		'Landing Time:': 'Landing Time:',
		Unit: 'Unit',
		From: 'From',
		'Launch Time': 'Launch Time',
		Command: 'Command',
		Status: 'Status',
		Send: 'Send',
		'Error fetching village groups!': 'Error fetching village groups!',
		'Choose Units to Snipe': 'Choose Units to Snipe',
		Group: 'Group',
		'No possible snipe options found!': 'No possible snipe options found!',
		Distance: 'Distance',
		'An error occured while fetching troop counts!': 'An error occured while fetching troop counts!',
		'snipe attempts found': 'snipe attempts found',
		'Nothing to export!': 'Nothing to export!',
		'Target:': 'Target:',
		'Send in': 'Send in',
		'Destination Village': 'Destination Village',
		Sigil: 'Sigil',
		'Min. Amount': 'Min. Amount',
		'Export Config': 'Export Config',
		'Import Config': 'Import Config',
		'Configuration imported successfully!': 'Configuration imported successfully!',
		'Nothing to import!': 'Nothing to import!',
	},
	it_IT: {
		'Single Village Snipe': 'Snipe Singolo Villaggio',
		Help: 'Aiuto',
		'This script can only be run on a single village screen!':
			'Questo script puÃ² essere lanciato solo dalla panoramica del villaggio',
		'Landing Time': 'Tempo di arrivo',
		'Calculate Launch Times': 'Calcola tempi di lancio',
		'Export as BB Code': 'Esporta in BB code',
		'Landing time was updated!': 'Il tempo di arrivo Ã¨ stato aggiornato!',
		'Plan for:': 'Plan per:',
		'Landing Time:': 'Tempo di arrivo:',
		Unit: 'UnitÃ ',
		From: 'Da',
		'Launch Time': 'Tempo di lancio',
		Command: 'Comando',
		Status: 'Stato',
		Send: 'Invia',
		'Error fetching village groups!': 'Errore nel recupero gruppo!',
		'Choose Units to Snipe': `Scegli l'unitÃ  con cui ninjare`,
		Group: 'Gruppo',
		'No possible snipe options found!': 'Nessuna combinazione disponibile!',
		Distance: 'Distanza',
		'An error occured while fetching troop counts!': 'Errore nel recupero conteggio truppe!',
		'snipe attempts found': 'Ninjata possibile trovata',
		'Nothing to export!': `Non c'Ã¨ niente da esportare!`,
		'Target:': 'Target:',
		'Send in': 'Lancia tra',
		'Destination Village': 'Villaggio di destinazione',
		Sigil: 'Sigillo',
		'Min. Amount': 'Qnt. Minima',
		'Export Config': 'Esporta configurazione',
		'Import Config': 'Importa comfigurazione',
		'Configuration imported successfully!': 'Configurazione importat con successo!',
		'Nothing to import!': `Non c'Ã¨ nulla da importare!`,
	},
};

// Init Debug
initDebug();

// Init Translations Notice
initTranslationsNotice();

// Fetch unit info only when needed
if (LAST_UPDATED_TIME !== null) {
	if (Date.parse(new Date()) >= LAST_UPDATED_TIME + TIME_INTERVAL) {
		fetchUnitInfo();
	} else {
		unitInfo = JSON.parse(localStorage.getItem(`${LS_PREFIX}_unit_info`));
	}
} else {
	fetchUnitInfo();
}

// Initialize Attack Planner
async function initVillageSnipe(groupId) {
	// run on script load
	villages = await fetchAllPlayerVillagesByGroup(groupId);
	troopCounts = await fetchTroopsForCurrentGroup(groupId);
	const groups = await fetchVillageGroups();
	const unitsTable = buildUnitsChoserTable();
	const content = prepareContent(groups, unitsTable);
	renderUI(content);

	// after script has been loaded events
	setTimeout(function () {
		// set the default landing time
		const today = new Date().toLocaleString('en-GB').replace(',', '');
		jQuery('#raLandingTime').val(today);

		// set the default destination village
		const destinationVillage = jQuery('#content_value table table td:eq(2)').text();
		jQuery('#raDestinationVillage').val(destinationVillage);
	}, 100);

	// scroll to element to focus user's attention
	jQuery('html,body').animate({ scrollTop: jQuery('#raSingleVillageSnipe').offset().top - 8 }, 'slow');

	// action handlers
	calculateLaunchTimes();
	fillLandingTimeFromCommand();
	filterVillagesByChosenGroup();
	exportBBCode();
	exportConfig();
	importConfig();
}

// Helper: Prepare UI
function prepareContent(groups, unitsTable) {
	const groupsFilter = renderGroupsFilter(groups);

	return `
		<div class="ra-mb15">
			<div class="ra-grid">
				<div>
					<label for="raDestinationVillage">
						${tt('Destination Village')}
					</label>
					<input id="raDestinationVillage" type="text" value="">
				</div>
				<div>
					<label for="raLandingTime">
						${tt('Landing Time')} (dd/mm/yyyy HH:mm:ss)
					</label>
					<input id="raLandingTime" type="text" value="">
				</div>
				<div>
					<label for="raLandingTime">
						${tt('Sigil')}
					</label>
					<input id="raSigil" type="text" value="0">
				</div>
				<div>
					<label>${tt('Min. Amount')}</label>
					<input id="raMinAmount" type="text" value="50">
				</div>
				<div>
					<label>${tt('Group')}</label>
					${groupsFilter}
				</div>
			</div>
		</div>
		<div class="ra-mb15">
			<label>${tt('Choose Units to Snipe')}</label>
			${unitsTable}
		</div>
		<div class="ra-mb15">
			<a href="javascript:void(0);" id="calculateLaunchTimes" class="btn btn-confirm-yes">
				${tt('Calculate Launch Times')}
			</a>
			<a href="javascript:void(0);" id="exportBBCodeBtn" class="btn" data-snipe="">
				${tt('Export as BB Code')}
			</a>
			<a href="javascript:void(0);" id="exportConfig" class="btn">
				${tt('Export Config')}
			</a>
			<a href="javascript:void(0);" id="importConfig" class="btn">
				${tt('Import Config')}
			</a>
		</div>
		<div style="display:none;" class="ra-mb15" id="raPossibleCombinations">
			<label><span id="possibleCombinationsCount">0</span> ${tt('snipe attempts found')}</label>
			<div id="possibleCombinationsTable"></div>
		</div>
	`;
}

// Render UI
function renderUI(body) {
	const content = `
        <div class="ra-single-village-snipe" id="raSingleVillageSnipe">
            <h2>${tt(scriptData.name)}</h2>
            <div class="ra-single-village-snipe-data">
                ${body}
            </div>
            <br>
            <small>
                <strong>
                    ${tt(scriptData.name)} ${scriptData.version}
                </strong> -
                <a href="${scriptData.authorUrl}" target="_blank" rel="noreferrer noopener">
                    ${scriptData.author}
                </a> -
                <a href="${scriptData.helpLink}" target="_blank" rel="noreferrer noopener">
                    ${tt('Help')}
                </a>
            </small>
        </div>
        <style>
            .ra-single-village-snipe { position: relative; display: block; width: auto; height: auto; clear: both; margin: 0 auto 15px; padding: 10px; border: 1px solid #603000; box-sizing: border-box; background: #f4e4bc; }
			.ra-single-village-snipe * { box-sizing: border-box; }
			.ra-single-village-snipe input[type="text"] { width: 100%; padding: 5px 10px; border: 1px solid #000; font-size: 16px; line-height: 1; }
			.ra-single-village-snipe label { font-weight: 600 !important; margin-bottom: 5px; display: block; }
			.ra-single-village-snipe select { width: 100%; padding: 5px 10px; border: 1px solid #000; font-size: 16px; line-height: 1; }
			.ra-single-village-snipe .btn-confirm-yes { padding: 3px; }
			.ra-single-village-snipe .ra-grid { display: grid; grid-template-columns: 150px 1fr 100px 150px 150px; grid-gap: 0 20px; }
			/* Normal Table */
			.ra-table { border-collapse: separate !important; border-spacing: 2px !important; }
			.ra-table label,
			.ra-table input { cursor: pointer; margin: 0; }
			.ra-table th { font-size: 14px; }
			.ra-table th,
            .ra-table td { padding: 4px; text-align: center; }
            .ra-table td a { word-break: break-all; }
			.ra-table tr:nth-of-type(2n+1) td { background-color: #fff5da; }
			.ra-table a:focus:not(a.btn) { color: blue; }
			/* Popup Content */
			.ra-popup-content { position: relative; display: block; width: 360px; }
			.ra-popup-content * { box-sizing: border-box; }
			.ra-popup-content label { font-weight: 600 !important; margin-bottom: 5px; display: block; }
			.ra-popup-content textarea { width: 100%; height: 100px; resize: none; }
			/* Helpers */
			.ra-mb15 { margin-bottom: 15px; }
			.ra-mb30 { margin-bottom: 30px; }
			.ra-chosen-command td { background-color: #ffe563 !important; }
			.ra-text-left { text-align: left !important; }
			.ra-text-center { text-align: center !important; }
			.ra-unit-count { display: inline-block; margin-top: 3px; vertical-align: top; }
        </style>
    `;

	if (jQuery('.ra-single-village-snipe').length < 1) {
		jQuery('#contentContainer').prepend(content);
	} else {
		jQuery('.ra-single-village-snipe-data').html(body);
	}
}

// Action Handler: Export Config
function exportConfig() {
	jQuery('#exportConfig').on('click', function (e) {
		const destinationVillage = jQuery('#raDestinationVillage').val();
		const landingTime = jQuery('#raLandingTime').val();
		const sigil = jQuery('#raSigil').val();
		const minAmount = jQuery('#raMinAmount').val();

		const data = {
			destinationVillage: destinationVillage,
			landingTime: landingTime,
			sigil: sigil,
			minAmount: minAmount,
		};

		const content = `
			<div class="ra-popup-content">
				<textarea readonly>${JSON.stringify(data)}</textarea>
			</div>
		`;
		Dialog.show('content', content);
	});
}

// Action Handler: Export Config
function importConfig() {
	jQuery('#importConfig').on('click', function (e) {
		const content = `
			<div class="ra-popup-content">
				<textarea id="importConfigField"></textarea>
				<a href="javascript:void(0);" id="importConfigBtn" class="btn">${tt('Import Config')}</a>
			</div>
		`;
		Dialog.show('content', content);

		jQuery('#importConfigBtn').on('click', function (e) {
			e.preventDefault();
			const config = jQuery('#importConfigField').val();
			if (config.length) {
				const data = JSON.parse(config);
				const { destinationVillage, landingTime, minAmount, sigil } = data;
				jQuery('#raDestinationVillage').val(destinationVillage);
				jQuery('#raLandingTime').val(landingTime);
				jQuery('#raSigil').val(sigil);
				jQuery('#raMinAmount').val(minAmount);
				jQuery('#calculateLaunchTimes').trigger('click');
				UI.SuccessMessage('Configuration imported successfully!');
			} else {
				UI.ErrorMessage(tt('Nothing to import!'));
			}
		});
	});
}

// Action Handler: Grab the "chosen" villages and calculate their launch times based on the unit type
function calculateLaunchTimes() {
	jQuery('#calculateLaunchTimes').on('click', function (e) {
		e.preventDefault();

		// collect user input and destination village
		const landingTimeString = jQuery('#raLandingTime').val().trim();
		const destinationVillage = jQuery('#raDestinationVillage').val().trim();
		const minAmount = parseInt(jQuery('#raMinAmount').val().trim());
		const chosenUnits = [];

		jQuery('.ra-unit-selector').each(function () {
			if (jQuery(this).is(':checked')) {
				chosenUnits.push(this.value);
			}
		});

		if (DEBUG) {
			console.debug(`${scriptInfo()} landingTimeString:`, landingTimeString);
			console.debug(`${scriptInfo()} destinationVillage:`, destinationVillage);
			console.debug(`${scriptInfo()} minAmount:`, minAmount);
			console.debug(`${scriptInfo()} chosenUnits:`, chosenUnits);
		}

		// helper variables
		const landingTime = getLandingTime(landingTimeString);
		const serverTime = getServerTime();

		const possibleSnipes = [];
		const realSnipes = [];

		villages.forEach((village) => {
			const { id, name, coords } = village;
			const distance = calculateDistance(coords, destinationVillage);

			chosenUnits.forEach((unit) => {
				const launchTime = getLaunchTime(unit, landingTime, distance);
				if (launchTime > serverTime.getTime()) {
					const formattedLaunchTime = formatDateTime(launchTime);
					if (distance > 0) {
						possibleSnipes.push({
							id: id,
							name: name,
							unit: unit,
							coords: coords,
							distance: distance,
							launchTime: launchTime,
							formattedLaunchTime: formattedLaunchTime,
						});
					}
				}
			});
		});

		possibleSnipes.sort((a, b) => {
			return a.launchTime - b.launchTime;
		});

		if (DEBUG) {
			console.debug(`${scriptInfo()} possibleSnipes:`, possibleSnipes);
			console.debug(`${scriptInfo()} troopCounts:`, troopCounts);
		}

		// filter possible snipes to only show villages with available units
		possibleSnipes.forEach((snipe) => {
			const { id, unit } = snipe;
			troopCounts.forEach((villageTroops) => {
				if (villageTroops.villageId === id) {
					if (villageTroops[unit] >= minAmount) {
						snipe = {
							...snipe,
							unitAmount: villageTroops[unit],
						};
						realSnipes.push(snipe);
					} else {
						if (villageTroops['snob'] > 0) {
							snipe = {
								...snipe,
								unitAmount: villageTroops[unit],
							};
							realSnipes.push(snipe);
						}
					}
				}
			});
		});

		if (realSnipes.length > 0) {
			const snipeCombinationsTable = buildCombinationsTable(realSnipes, destinationVillage);
			jQuery('#raPossibleCombinations').show();
			jQuery('#possibleCombinationsCount').text(realSnipes.length);
			jQuery('#possibleCombinationsTable').html(snipeCombinationsTable);
			jQuery('#exportBBCodeBtn').attr('data-snipe', JSON.stringify(realSnipes));
			Timing.tickHandlers.timers.init();
		} else {
			UI.ErrorMessage(tt('No possible snipe options found!'));
			jQuery('#raPossibleCombinations').hide();
			jQuery('#possibleCombinationsCount').text(0);
			jQuery('#possibleCombinationsTable').html('');
			jQuery('#exportBBCodeBtn').attr('data-snipe', '');
		}
	});
}

// Action Handler: When a command is clicked fill landing time with the landing time of the command
function fillLandingTimeFromCommand() {
	jQuery('#commands_outgoings table tbody tr.command-row, #commands_incomings table tbody tr.command-row').on(
		'click',
		function () {
			jQuery('#commands_outgoings table tbody tr.command-row').removeClass('ra-chosen-command');
			jQuery(this).addClass('ra-chosen-command');

			const commandLandingTime = parseInt(jQuery(this).find('td:eq(2) span').attr('data-endtime')) * 1000;

			const landingTimeDateTime = new Date(commandLandingTime);
			const serverDateTime = getServerTime();
			const localDateTime = new Date();

			const diffTime = Math.abs(localDateTime - serverDateTime);
			const newLandingTime = Math.ceil(Math.abs(landingTimeDateTime - diffTime));
			const newLandingTimeObj = new Date(newLandingTime);
			const formattedNewLandingTime = formatDateTime(newLandingTimeObj);

			jQuery('#raLandingTime').val(formattedNewLandingTime);
			UI.SuccessMessage(tt('Landing time was updated!'));
		}
	);
}

// Action Handler: Filter villages shown by selected group
function filterVillagesByChosenGroup() {
	jQuery('#raGroupsFilter').on('change', function (e) {
		e.preventDefault();
		initVillageSnipe(e.target.value);
		localStorage.setItem(`${LS_PREFIX}_chosen_group`, e.target.value);
	});
}

// Action Handler: Export snipe attempts list as BB Code
function exportBBCode() {
	jQuery('#exportBBCodeBtn').on('click', function (e) {
		e.preventDefault();

		const snipeAttempts = jQuery(this).attr('data-snipe');
		if (snipeAttempts) {
			jQuery(this).addClass('btn-confirm-yes');
			const snipeAttemptsJSON = JSON.parse(snipeAttempts);
			const bbCodeSnipes = getBBCodeExport(snipeAttemptsJSON);
			const content = `
				<div class="ra-popup-content">
					<label for="exportBBCodeInput">${tt('Export as BB Code')}</label>
					<textarea readonly id="exportBBCodeInput">${bbCodeSnipes.trim()}</textarea>
				</div>
			`;
			Dialog.show('content', content);
		} else {
			UI.ErrorMessage(tt('Nothing to export!'));
		}
	});
}

// Prepare Units Selector
function buildUnitsChoserTable() {
	const units = game_data.units;

	let unitsTable = ``;

	let thUnits = ``;
	let tableRow = ``;

	units.forEach((unit) => {
		if (unit !== 'spy' && unit !== 'militia') {
			// automatically check defensive units
			let checked = '';
			if (unit === 'spear' || unit === 'sword' || unit === 'archer' || unit === 'heavy') {
				checked = `checked`;
			}

			thUnits += `
				<th class="ra-text-center">
					<label for="unit_${unit}">
						<img src="/graphic/unit/unit_${unit}.png">
					</label>
				</th>
			`;

			tableRow += `
				<td class="ra-text-center">
					<input name="ra_chosen_units" type="checkbox" ${checked} id="unit_${unit}" class="ra-unit-selector" value="${unit}" />
				</td>
			`;
		}
	});

	unitsTable = `
		<table class="ra-table vis" width="100%" id="raUnitSelector">
			<thead>
				<tr>
					${thUnits}
				</tr>
			</thead>
			<tbody>
				<tr>
					${tableRow}
				</tr>
			</tbody>
		</table>
	`;

	return unitsTable;
}

// Render Combinations Table
function buildCombinationsTable(snipes, destinationVillage) {
	let combinationsTable = `
		<table class="ra-table vis" width="100%">
			<thead>
				<tr>
					<th>
						#
					</th>
					<th class="ra-text-left">
						${tt('From')}
					</th>
					<th>
						${tt('Unit')}
					</th>
					<th>
						${tt('Distance')}
					</th>
					<th>
						${tt('Launch Time')}
					</th>
					<th>
						${tt('Send in')}
					</th>
					<th>
						${tt('Send')}
					</th>
				</tr>
			</thead>
			<tbody>
	`;

	const serverTime = getServerTime().getTime();

	snipes.forEach((snipe, index) => {
		const { id, name, coords, unit, distance, launchTime, formattedLaunchTime, unitAmount } = snipe;
		const [toX, toY] = destinationVillage.split('|');
		const continent = getContinentByCoord(coords);
		const timeTillLaunch = secondsToHms((launchTime - serverTime) / 1000);

		let commandUrl = '';
		if (game_data.player.sitter > 0) {
			commandUrl = `/game.php?t=${game_data.player.id}&village=${id}&screen=place&x=${toX}&y=${toY}&${unit}=${unitAmount}`;
		} else {
			commandUrl = `/game.php?village=${id}&screen=place&x=${toX}&y=${toY}&y=${toY}&${unit}=${unitAmount}`;
		}

		combinationsTable += `
			<tr>
				<td>
					${index + 1}
				</td>
				<td class="ra-text-left">
					<a href="${game_data.link_base_pure}info_village&id=${id}" target="_blank" rel="noopener noreferrer">
						${name} (${coords}) K${continent}
					</a>
				</td>
				<td>
					<img src="/graphic/unit/unit_${unit}.png" /> <span class="ra-unit-count">${formatAsNumber(unitAmount)}</span>
				</td>
				<td>
					${parseFloat(distance).toFixed(2)}
				</td>
				<td>
					${formattedLaunchTime}
				</td>
				<td>
					<span class="timer">${timeTillLaunch}</span>
				</td>
				<td>
					<a href="${commandUrl}" target="_blank" rel="noopener noreferrer" class="btn">
						${tt('Send')}
					</a>
				</td>
			</tr>
		`;
	});

	combinationsTable += `
			</tbody>
		</table>
	`;

	return combinationsTable;
}

// Helper: Convert Seconds to Hour:Minutes:Seconds
function secondsToHms(timestamp) {
	const hours = Math.floor(timestamp / 60 / 60);
	const minutes = Math.floor(timestamp / 60) - hours * 60;
	const seconds = timestamp % 60;
	const formatted =
		hours.toString().padStart(2, '0') +
		':' +
		minutes.toString().padStart(2, '0') +
		':' +
		seconds.toString().padStart(2, '0');
	return formatted;
}

// Helper: Get BB Code export for snipe attempts
function getBBCodeExport(snipes) {
	const landingTime = jQuery('#raLandingTime').val().trim();
	const destinationVillage = jQuery('#raDestinationVillage').val().trim();

	let bbCode = `[size=12][b]${tt('Target:')}[/b] ${destinationVillage}\n[b]${tt(
		'Landing Time:'
	)}[/b] ${landingTime}[/size]\n\n`;
	bbCode += `[table][**]${tt('Unit')}[||]${tt('From')}[||]${tt('Launch Time')}[||]${tt('Command')}[||]${tt(
		'Status'
	)}[/**]\n`;

	snipes.forEach((plan) => {
		const { coords, formattedLaunchTime, id, unit, unitAmount } = plan;

		const [toX, toY] = destinationVillage.split('|');

		let commandUrl = '';

		if (game_data.player.sitter > 0) {
			commandUrl = `/game.php?t=${game_data.player.id}&village=${id}&screen=place&x=${toX}&y=${toY}&${unit}=${unitAmount}`;
		} else {
			commandUrl = `/game.php?village=${id}&screen=place&x=${toX}&y=${toY}&${unit}=${unitAmount}`;
		}

		bbCode += `[*][unit]${unit}[/unit] ${formatAsNumber(
			unitAmount
		)}[|] ${coords} [|]${formattedLaunchTime}[|][url=${window.location.origin}${commandUrl}]${tt(
			'Send'
		)}[/url][|]\n`;
	});

	bbCode += `[/table]`;
	return bbCode;
}

// Helper: Process coordinate and extract coordinate continent
function getContinentByCoord(coord) {
	if (!coord) return '';
	const coordParts = coord.split('|');
	return coordParts[1].charAt(0) + coordParts[0].charAt(0);
}

// Helper: Calculate distance between 2 villages
function calculateDistance(from, to) {
	const [x1, y1] = from.split('|');
	const [x2, y2] = to.split('|');
	const deltaX = Math.abs(x1 - x2);
	const deltaY = Math.abs(y1 - y2);
	return Math.sqrt(deltaX * deltaX + deltaY * deltaY);
}

// Helper: Get launch time of command
function getLaunchTime(unit, landingTime, distance) {
	const msPerSec = 1000;
	const secsPerMin = 60;
	const msPerMin = msPerSec * secsPerMin;

	const sigilPercentage = +jQuery('#raSigil').val();
	const sigilRatio = 1 + sigilPercentage / 100;

	const unitSpeed = unitInfo.config[unit].speed;
	const unitTime = (distance * unitSpeed * msPerMin) / sigilRatio;

	const launchTime = new Date();
	launchTime.setTime(Math.round((landingTime - unitTime) / msPerSec) * msPerSec);

	return launchTime.getTime();
}

// Helper: Get server time
function getServerTime() {
	const serverTime = jQuery('#serverTime').text();
	const serverDate = jQuery('#serverDate').text();

	const [day, month, year] = serverDate.split('/');
	const serverTimeFormatted = year + '-' + month + '-' + day + ' ' + serverTime;
	const serverTimeObject = new Date(serverTimeFormatted);

	return serverTimeObject;
}

// Helper: Format date and time
function formatDateTime(date) {
	let currentDateTime = new Date(date);

	var currentYear = currentDateTime.getFullYear();
	var currentMonth = currentDateTime.getMonth();
	var currentDate = currentDateTime.getDate();
	var currentHours = '' + currentDateTime.getHours();
	var currentMinutes = '' + currentDateTime.getMinutes();
	var currentSeconds = '' + currentDateTime.getSeconds();

	currentMonth = currentMonth + 1;
	currentMonth = '' + currentMonth;
	currentMonth = currentMonth.padStart(2, '0');

	currentHours = currentHours.padStart(2, '0');
	currentMinutes = currentMinutes.padStart(2, '0');
	currentSeconds = currentSeconds.padStart(2, '0');

	let formatted_date =
		currentDate +
		'/' +
		currentMonth +
		'/' +
		currentYear +
		' ' +
		currentHours +
		':' +
		currentMinutes +
		':' +
		currentSeconds;

	return formatted_date;
}

// Helper: Get landing time date object
function getLandingTime(landingTime) {
	const [landingDay, landingHour] = landingTime.split(' ');
	const [day, month, year] = landingDay.split('/');
	const landingTimeFormatted = year + '-' + month + '-' + day + ' ' + landingHour;
	const landingTimeObject = new Date(landingTimeFormatted);
	return landingTimeObject;
}

// Helper: Render groups filter
function renderGroupsFilter(groups) {
	const groupId = localStorage.getItem(`${LS_PREFIX}_chosen_group`);
	let groupsFilter = `
		<select name="ra_groups_filter" id="raGroupsFilter">
	`;

	for (const [_, group] of Object.entries(groups.result)) {
		const { group_id, name } = group;
		const isSelected = parseInt(group_id) === parseInt(groupId) ? 'selected' : '';
		if (name !== undefined) {
			groupsFilter += `
				<option value="${group_id}" ${isSelected}>
					${name}
				</option>
			`;
		}
	}

	groupsFilter += `
		</select>
	`;

	return groupsFilter;
}

// Helper: Fetch player villages by group
async function fetchAllPlayerVillagesByGroup(groupId) {
	const villagesByGroup = await jQuery
		.post({
			url: game_data.link_base_pure + 'groups&ajax=load_villages_from_group',
			data: { group_id: groupId },
			dataType: 'json',
			headers: { 'TribalWars-Ajax': 1 },
		})
		.then(({ response }) => {
			const parser = new DOMParser();
			const htmlDoc = parser.parseFromString(response.html, 'text/html');
			const tableRows = jQuery(htmlDoc).find('#group_table > tbody > tr').not(':eq(0)');

			let villagesList = [];

			tableRows.each(function () {
				const villageId = parseInt(jQuery(this).find('td:eq(0) a').attr('href').split('(')[1].split(',')[0]);
				const villageName = jQuery(this).find('td:eq(0)').text().trim();
				const villageCoords = jQuery(this).find('td:eq(1)').text().trim();

				villagesList.push({
					id: villageId,
					name: villageName,
					coords: villageCoords,
				});
			});

			return villagesList;
		});

	return villagesByGroup;
}

// Helper: Fetch village groups
async function fetchVillageGroups() {
	const villageGroups = await jQuery
		.get(game_data.link_base_pure + 'groups&mode=overview&ajax=load_group_menu')
		.then((response) => response)
		.catch((error) => {
			UI.ErrorMessage('Error fetching village groups!');
			console.error(`${scriptInfo()} Error:`, error);
		});

	return villageGroups;
}

// Helper: Fetch World Unit Info
function fetchUnitInfo() {
	jQuery
		.ajax({
			url: '/interface.php?func=get_unit_info',
		})
		.done(function (response) {
			unitInfo = xml2json($(response));
			localStorage.setItem(`${LS_PREFIX}_unit_info`, JSON.stringify(unitInfo));
			localStorage.setItem(`${LS_PREFIX}_last_updated`, Date.parse(new Date()));
		});
}

// Helper: Fetch home troop counts for current group
async function fetchTroopsForCurrentGroup() {
	const groupId = jQuery('.ra-group-filter.btn-confirm-yes').attr('data-group-id');
	const troopsForGroup = await jQuery
		.get(game_data.link_base_pure + `overview_villages&mode=combined&group=${groupId}&`)
		.then((response) => {
			const htmlDoc = jQuery.parseHTML(response);
			const combinedTableRows = jQuery(htmlDoc).find('#combined_table tr.nowrap');
			const combinedTableHead = jQuery(htmlDoc).find('#combined_table tr:eq(0) th');

			const homeTroops = [];
			const combinedTableHeader = [];

			// collect possible buildings and troop types
			jQuery(combinedTableHead).each(function () {
				const thImage = jQuery(this).find('img').attr('src');
				if (thImage) {
					let thImageFilename = thImage.split('/').pop();
					thImageFilename = thImageFilename.replace('.png', '');
					combinedTableHeader.push(thImageFilename);
				} else {
					combinedTableHeader.push(null);
				}
			});

			// collect possible troop types
			combinedTableRows.each(function () {
				let rowTroops = {};

				combinedTableHeader.forEach((tableHeader, index) => {
					if (tableHeader) {
						if (tableHeader.includes('unit_')) {
							const villageId = jQuery(this).find('td:eq(1) span.quickedit-vn').attr('data-id');
							const unitType = tableHeader.replace('unit_', '');
							rowTroops = {
								...rowTroops,
								villageId: parseInt(villageId),
								[unitType]: parseInt(jQuery(this).find(`td:eq(${index})`).text()),
							};
						}
					}
				});

				homeTroops.push(rowTroops);
			});

			return homeTroops;
		})
		.catch((error) => {
			UI.ErrorMessage(tt('An error occured while fetching troop counts!'));
			console.error(`${scriptInfo()} Error:`, error);
		});

	return troopsForGroup;
}

// Helper: Format as number
function formatAsNumber(number) {
	return parseInt(number).toLocaleString('de');
}

// Helper: XML to JSON converter
var xml2json = function ($xml) {
	var data = {};
	$.each($xml.children(), function (i) {
		var $this = $(this);
		if ($this.children().length > 0) {
			data[$this.prop('tagName')] = xml2json($this);
		} else {
			data[$this.prop('tagName')] = $.trim($this.text());
		}
	});
	return data;
};

// Helper: Get parameter by name
function getParameterByName(name, url = window.location.href) {
	return new URL(url).searchParams.get(name);
}

// Helper: Generates script info
function scriptInfo() {
	return `[${scriptData.name} ${scriptData.version}]`;
}

// Helper: Prints universal debug information
function initDebug() {
	console.debug(`${scriptInfo()} It works ðŸš€!`);
	console.debug(`${scriptInfo()} HELP:`, scriptData.helpLink);
	if (DEBUG) {
		console.debug(`${scriptInfo()} Market:`, game_data.market);
		console.debug(`${scriptInfo()} World:`, game_data.world);
		console.debug(`${scriptInfo()} Screen:`, game_data.screen);
		console.debug(`${scriptInfo()} Game Version:`, game_data.majorVersion);
		console.debug(`${scriptInfo()} Game Build:`, game_data.version);
		console.debug(`${scriptInfo()} Locale:`, game_data.locale);
		console.debug(`${scriptInfo()} Premium:`, game_data.features.Premium.active);
	}
}

// Helper: Text Translator
function tt(string) {
	const gameLocale = game_data.locale;

	if (translations[gameLocale] !== undefined) {
		return translations[gameLocale][string];
	} else {
		return translations['en_DK'][string];
	}
}

// Helper: Translations Notice
function initTranslationsNotice() {
	const gameLocale = game_data.locale;

	if (translations[gameLocale] === undefined) {
		UI.ErrorMessage(
			`No translation found for <b>${gameLocale}</b>. <a href="${scriptData.helpLink}" class="btn" target="_blank" rel="noreferrer noopener">Add Yours</a> by replying to the thread.`,
			4000
		);
	}
}

// Initialize Script
(function () {
	const gameScreen = getParameterByName('screen');
	if (gameScreen === 'info_village') {
		initVillageSnipe(GROUP_ID);
	} else {
		UI.ErrorMessage(tt('This script can only be run on a single village screen!'));
	}
})();
