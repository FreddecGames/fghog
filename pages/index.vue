<template>
    <div class="h-100 w-100">

        <div v-if="popupText" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
            <div class="bg-2 rounded border p-3" style="width:380px; overflow-y:auto; overflow-x:hidden; max-height:642px;">
                <div class="row g-3">
                    <div class="col-12 text-center" v-html="popupText"></div>
                    <div class="col-6 text-start">
                        <button v-if="popupTextCancel" type="button" class="btn border" @click="popupTextCancelAction()">
                            <span>{{ popupTextCancel }}</span>
                        </button>
                    </div>
                    <div class="col-6 text-end">
                        <button v-if="popupTextOk" type="button" class="btn border" @click="popupTextOkAction()">
                            <span>{{ popupTextOk }}</span>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    
        <div v-if="isMobile == true" class="position-absolute bg-grid top-0 bottom-0 start-0 end-0 d-flex align-items-center justify-content-center">
            <div class="p-3">
                <div class="row g-2">
                    <div class="col-12 text-center"><img :src="require(`~/assets/ui/logo.png`)" width="48px" class="rounded" /></div>
                    <div class="col-12 text-white text-center h5">HoG by FG</div>
                    <div class="col-12 text-warning text-center">Your device is not supported for the moment.</div>
                    <div class="col-12 text-normal text-center">To be informed when your device will be supported and new features will be ready, please join Discord server.</div>
                    <div class="col-12 text-normal text-center">
                        <a href="https://discord.gg/3UkgeeT9CV" target="_blank" class="btn bg-bar border">
                            <img :src="require(`~/assets/ui/discord.png`)" width="16px" height="16px" alt="Discord" />
                            <span class="ms-2">Discord</span>
                        </a>
                    </div>                
                </div>
            </div>
        </div>
        
        <div v-if="isMobile == false && started == false" class="position-absolute bg-grid top-0 bottom-0 start-0 end-0 d-flex align-items-center justify-content-center">
            <div class="p-3">
                <div class="row g-2">
                    <div class="col-12 text-center"><img :src="require(`~/assets/ui/logo.png`)" width="48px" class="rounded" /></div>
                    <div class="col-12 text-white text-center h5">HoG by FG</div>
                    <div v-if="error == null" class="col-12 flicker text-warning text-center">Initializing game...</div>
                    <div v-if="error != null" class="col-12 text-center">
                        <div class="row g-2">
                            <div class="col-12 text-center text-danger">
                                <div class="">An error occured during game loading</div>
                                <div class="opacity-75">"{{ this.error }}"</div>
                            </div>
                            <div class="col-12">
                                <span class="text-normal">To ask for help, you could contact <span class="text-white">Freddec</span> on Discord with following exported data</span>
                            </div>
                            <div class="col-12">
                                <a href="https://discord.gg/3UkgeeT9CV" target="_blank" class="btn bg-bar border">
                                    <img :src="require(`~/assets/ui/discord.png`)" width="16px" height="16px" alt="Discord" />
                                    <span class="ms-2">Discord</span>
                                </a>
                            </div>
                            <div class="col-12">
                                <textarea spellcheck="false" rows="5" class="w-100 rounded bg-1 border text-normal p-2">{{ exportGameData }}</textarea>
                            </div>
                            <div class="col-12">
                                <span class="text-normal">Or you could wipe your local data to restart the game at the beginning</span>
                            </div>
                            <div class="col-12">
                                <button type="button" class="btn bg-bar border text-danger" @click="resetGameData()">
                                    <span><i class="fas fa-fw fa-skull"></i></span>
                                    <span class="ms-2">Wipe Local Data</span>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div v-if="isMobile == false && started == true" class="position-absolute bg-grid top-0 bottom-0 start-0 end-0">
            
            <div class="w-100 h-100 d-flex align-items-center justify-content-center">
                <div class="w-100 h-100 position-relative border rounded" style="max-width:1160px; max-height:728px;">
                
                    <div v-if="popupMoveFleet" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
                        <div class="bg-2 rounded border p-3">
                            <div class="row g-3">
                                <div class="col-12">
                                    <div class="text-primary text-center h5">Move <span class="text-white">{{ popupMoveFleet.name }}</span> to</div>
                                </div>
                                <div class="col-12">
                                    <div class="row align-items-center">
                                        <div class="col">
                                            <span class="text-gray">Select a destination</span>
                                        </div>
                                        <div class="col-auto dropdown">
                                            <button type="button" class="btn border bg-3 text-start dropdown-toggle" style="width:165px;" data-bs-toggle="dropdown" aria-expanded="false">
                                                <span v-if="popupMoveDestination != null">{{ $t('planetName_' + popupMoveDestination.ref.id) }}</span>
                                                <span v-if="popupMoveDestination == null">No destination selected</span>
                                            </button>
                                            <div class="dropdown-menu border" style="max-height:200px; width:165px; overflow-y:auto;">
                                                <button v-for="planet in currentNebula.getVisiblePlanets()" :key="planet.ref.id" type="button" class="text-start dropdown-item" @click="popupMoveDestination = planet">
                                                    <span>{{ $t('planetName_' + planet.ref.id) }}</span>
                                                </button>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-6 text-start">
                                    <button type="button" class="btn border" @click="popupMoveFleet = null">
                                        <span>Cancel</span>
                                    </button>
                                </div>
                                <div class="col-6 text-end">
                                    <button type="button" class="btn border" :class="{ 'disabled':popupMoveDestination == null }" @click="popupMoveFleet.move(popupMoveFleet.planet.ref.id, popupMoveDestination.ref.id); popupMoveFleet = null; currentFleetsOrbitting = null;">
                                        <span>Move</span>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="popupRenameFleet" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
                        <div class="bg-2 rounded border p-3">
                            <div class="row g-3">
                                <div class="col-12">
                                    <div class="text-primary text-center h5">Rename <span class="text-white">{{ popupRenameFleet.name }}</span> to</div>
                                </div>
                                <div class="col-12">
                                    <div class="row">
                                        <div class="col">
                                            <span class="text-gray">Type a new name</span>
                                        </div>
                                        <div class="col-auto">
                                            <input type="text" class="form-control text-normal border bg-3" v-model="popupRenameName" />
                                        </div>
                                    </div>
                                </div>
                                <div class="col-6 text-start">
                                    <button type="button" class="btn border" @click="popupRenameFleet = null">
                                        <span>Cancel</span>
                                    </button>
                                </div>
                                <div class="col-6 text-end">
                                    <button type="button" class="btn border" :class="{ 'disabled':popupRenameName == null || popupRenameName.length < 4 }" @click="popupRenameFleet.rename(popupRenameName); popupRenameFleet = null;">
                                        <span>Rename</span>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="popupAttackFleet" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
                        <div class="bg-2 rounded border p-3">
                            <div class="row g-3">
                                <div class="col-12">
                                    <div class="text-primary text-center h5">Attack with <span class="text-white">{{ popupAttackFleet.name }}</span> against</div>
                                </div>
                                <div class="col-12">
                                    <div class="row">
                                        <div class="col">
                                            <span class="text-gray">Select an enemy fleet</span>
                                        </div>
                                        <div class="col-auto dropdown">
                                            <button type="button" class="btn border bg-3 text-start dropdown-toggle" style="width:165px;" data-bs-toggle="dropdown" aria-expanded="false">
                                                <span v-if="popupAttackEnemyFleet != null">{{ popupAttackEnemyFleet.name }}</span>
                                                <span v-if="popupAttackEnemyFleet == null">No fleet selected</span>
                                            </button>
                                            <div class="dropdown-menu border" style="max-height:200px; width:165px; overflow-y:auto;">
                                                <button v-for="(fleet, index) in popupAttackFleet.planet.getEnemyFleets()" :key="index" type="button" class="text-start dropdown-item" @click="popupAttackEnemyFleet = fleet">
                                                    <span>{{ fleet.name }}</span>
                                                </button>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-6 text-start">
                                    <button type="button" class="btn border" @click="popupAttackFleet = null">
                                        <span>Cancel</span>
                                    </button>
                                </div>
                                <div class="col-6 text-end">
                                    <button type="button" class="btn border" :class="{ 'disabled':popupAttackEnemyFleet == null }" @click="makeBattle(popupAttackFleet, popupAttackEnemyFleet); popupAttackFleet = null;">
                                        <span>Attack</span>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="popupLoadFleet" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
                        <div class="bg-2 rounded border p-3" style="width:500px; overflow-y:auto; overflow-x:hidden; max-height:728px;">
                            <div class="row g-3">
                                <div class="col-12">
                                    <div class="text-primary text-center h5">Load resources into <span class="text-white">{{ popupLoadFleet.name }}</span></div>
                                </div>
                                <div class="col-12">
                                    <div class="row">
                                        <div v-for="resource in popupLoadFleet.planet.getAvailableRessources()" :key="resource.ref.id" class="col-12">
                                            <div class="row gx-2 align-items-center">
                                                <div class="col"><span class="text-white">{{ $t('resName_' + resource.ref.id) }}</span></div>
                                                <div class="col-auto"><span class="text-normal"><FormatNumber :value="resource.count" /></span></div>
                                                <div class="col-auto"><input type="number" class="form-control text-normal border bg-3" min="0" :max="resource.count" v-model.number="popupLoadResources[resource.ref.id]" :change="refreshPopupLoadCurrentStorage()" style="width:100px;"/></div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-12">
                                    <div class="row">
                                        <div class="col">
                                            <span class="text-normal">Storage</span>
                                        </div>
                                        <div class="col-auto">
                                            <span :class="{ 'text-white':popupLoadCurrentStorage <= popupLoadFleet.getTotalStorage(), 'text-danger':popupLoadCurrentStorage > popupLoadFleet.getTotalStorage() }"><FormatNumber :value="popupLoadCurrentStorage" /></span>
                                            <small class="text-gray">/<FormatNumber :value="popupLoadFleet.getTotalStorage()" /></small>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-6 text-start">
                                    <button type="button" class="btn border" @click="popupLoadFleet = null">
                                        <span>Cancel</span>
                                    </button>
                                </div>
                                <div class="col-6 text-end">
                                    <button type="button" class="btn border" :class="{ 'disabled':popupLoadCurrentStorage > popupLoadFleet.getTotalStorage() }" @click="popupLoadFleet.load(popupLoadResources); popupLoadFleet = null;">
                                        <span>Load</span>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="popupUnloadFleet" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
                        <div class="bg-2 rounded border p-3" style="width:500px; overflow-y:auto; overflow-x:hidden; max-height:728px;">
                            <div class="row g-3">
                                <div class="col-12">
                                    <div class="text-primary text-center h5">Unload resources from <span class="text-white">{{ popupUnloadFleet.name }}</span></div>
                                </div>
                                <div class="col-12">
                                    <div class="row">
                                        <div v-for="resource in popupUnloadFleet.getAvailableResources()" :key="resource.id" class="col-12">
                                            <div class="row gx-2 align-items-center">
                                                <div class="col"><span class="text-white">{{ $t('resName_' + resource.id) }}</span></div>
                                                <div class="col-auto"><span class="text-normal"><FormatNumber :value="resource.count" /></span></div>
                                                <div class="col-auto"><input type="number" class="form-control text-normal border bg-3" min="0" :max="resource.count" v-model.number="popupUnloadResources[resource.id]" :change="refreshPopupUnloadCurrentStorage()" style="width:100px;"/></div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-12">
                                    <div class="row">
                                        <div class="col">
                                            <span class="text-normal">Storage</span>
                                        </div>
                                        <div class="col-auto">
                                            <span :class="{ 'text-white':popupUnloadCurrentStorage >= 0, 'text-danger':popupUnloadCurrentStorage < 0 }"><FormatNumber :value="popupUnloadCurrentStorage" /></span>
                                            <small class="text-gray">/<FormatNumber :value="popupUnloadFleet.getTotalStorage()" /></small>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-6 text-start">
                                    <button type="button" class="btn border" @click="popupUnloadFleet = null">
                                        <span>Cancel</span>
                                    </button>
                                </div>
                                <div class="col-6 text-end">
                                    <button type="button" class="btn border" :class="{ 'disabled':popupUnloadCurrentStorage < 0 }" @click="popupUnloadFleet.unload(popupUnloadResources); popupUnloadFleet = null;">
                                        <span>Unload</span>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="popupAutorouteFleet" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
                        <div class="bg-2 rounded border p-3" style="width:675px; overflow-y:auto; overflow-x:hidden; max-height:642px;">
                            <div class="row g-3">
                                <div class="col-12">
                                    <div class="text-primary text-center h5">Create autoroute with <span class="text-white">{{ popupAutorouteFleet.name }}</span></div>
                                </div>
                                <div class="col-12">
                                    <div class="row">
                                        <div class="col-6 border-end">
                                            <div class="row align-items-center mb-2" style="height:32px;">
                                                <div class="col">
                                                    <span class="text-gray">Origin</span>
                                                </div>
                                                <div class="col-auto">
                                                    <span class="text-white">{{ $t('planetName_' + popupAutorouteFleet.planet.ref.id) }}</span>
                                                </div>
                                            </div>
                                            <div v-for="(value, id) in popupAutorouteOriginPercentages" :key="id" class="row gx-2 align-items-center">
                                                <div class="col"><span class="text-normal">{{ $t('resName_' + id) }}</span></div>
                                                <div class="col-auto d-flex align-items-center">
                                                    <input type="number" class="form-control text-white border bg-3" min="0" max="100" v-model.number="popupAutorouteOriginPercentages[id]" :change="refreshPopupAutorouteTotalOrigin()" style="width:100px;"/>
                                                    <span class="ms-1 text-normal">%</span>
                                                </div>
                                                <div class="col-auto text-end" style="width:50px;">
                                                    <span class="medium text-gray"><FormatNumber :value="popupAutorouteOriginPercentages[id] / 100 * popupAutorouteFleet.getTotalStorage()" /></span>
                                                </div>
                                            </div>
                                            <div class="row gx-2 align-items-center" style="height:32px;">
                                                <div class="col"><span class="text-gray">Fleet Storage</span></div>
                                                <div class="col-auto">
                                                    <span :class="{ 'text-success':popupAutorouteTotalOrigin <= popupAutorouteFleet.getTotalStorage(), 'text-danger':popupAutorouteTotalOrigin > popupAutorouteFleet.getTotalStorage() }"><FormatNumber :value="popupAutorouteTotalOrigin" /></span>
                                                    <span class="text-gray medium">/<FormatNumber :value="popupAutorouteFleet.getTotalStorage()" /></span>
                                                </div>
                                            </div>
                                        </div>
                                        <div class="col-6">
                                            <div class="row align-items-center mb-2">
                                                <div class="col">
                                                    <span class="text-gray">Destination</span>
                                                </div>
                                                <div class="col-auto dropdown">
                                                    <button type="button" class="btn border bg-3 text-white text-end dropdown-toggle" style="width:165px;" data-bs-toggle="dropdown" aria-expanded="false">
                                                        <span v-if="popupAutorouteDestination != null">{{ $t('planetName_' + popupAutorouteDestination.ref.id) }}</span>
                                                        <span v-if="popupAutorouteDestination == null">No destination selected</span>
                                                    </button>
                                                    <div class="dropdown-menu border" style="max-height:200px; width:165px; overflow-y:auto;">
                                                        <button v-for="planet in currentNebula.getVisiblePlanets()" :key="planet.ref.id" type="button" class="text-start dropdown-item" @click="popupAutorouteDestination = planet">
                                                            <span>{{ $t('planetName_' + planet.ref.id) }}</span>
                                                        </button>
                                                    </div>
                                                </div>
                                            </div>
                                            <div v-for="(value, id) in popupAutorouteDestinationPercentages" :key="id" class="row align-items-center">
                                                <div class="col"><span class="text-normal">{{ $t('resName_' + id) }}</span></div>
                                                <div class="col-auto d-flex align-items-center">
                                                    <input type="number" class="form-control text-white border bg-3" min="0" max="100" v-model.number="popupAutorouteDestinationPercentages[id]" :change="refreshPopupAutorouteTotalDestination()" style="width:100px;"/>
                                                    <span class="ms-1 text-normal">%</span>
                                                </div>
                                                <div class="col-auto text-end" style="width:50px;">
                                                    <span class="medium text-gray"><FormatNumber :value="popupAutorouteDestinationPercentages[id] / 100 * popupAutorouteFleet.getTotalStorage()" /></span>
                                                </div>
                                            </div>
                                            <div class="row gx-2 align-items-center" style="height:32px;">
                                                <div class="col"><span class="text-gray">Fleet Storage</span></div>
                                                <div class="col-auto">
                                                    <span :class="{ 'text-success':popupAutorouteTotalDestination <= popupAutorouteFleet.getTotalStorage(), 'text-danger':popupAutorouteTotalDestination > popupAutorouteFleet.getTotalStorage() }"><FormatNumber :value="popupAutorouteTotalDestination" /></span>
                                                    <span class="text-gray medium">/<FormatNumber :value="popupAutorouteFleet.getTotalStorage()" /></span>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-6 text-start">
                                    <button type="button" class="btn border" @click="popupAutorouteFleet = null">
                                        <span>Cancel</span>
                                    </button>
                                </div>
                                <div class="col-6 text-end">
                                    <button type="button" class="btn border" :class="{ 'disabled':popupAutorouteDestination == null || popupAutorouteTotalOrigin > popupAutorouteFleet.getTotalStorage() || popupAutorouteTotalDestination > popupAutorouteFleet.getTotalStorage() }" @click="popupAutorouteFleet.autoroute(popupAutorouteFleet.planet.ref.id, popupAutorouteDestination.ref.id, popupAutorouteOriginPercentages, popupAutorouteDestinationPercentages); popupAutorouteFleet = null; currentFleetsOrbitting = null;">
                                        <span>Create</span>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="toastText" class="position-absolute start-0 end-0" style="bottom:60px; z-index:100;">
                        <div class="d-flex justify-content-center">
                            <div class="toast show toast-body bg-2 text-center">
                                <span :class="{ 'text-normal':toastType == 'info', 'text-danger':toastType == 'error', 'text-success':toastType == 'success' }" v-html="toastText"></span>
                            </div>
                        </div>
                    </div>
                    
                    <div class="position-absolute top-0 start-0 end-0 border-bottom bg-1 d-flex align-items-center" style="height:50px;">
                        <div class="col-auto px-3" style="width:275px;">
                            <div class="row gx-2 align-items-baseline">
                                <span class="col-auto text-normal">Influence</span>
                                <span class="col-auto text-white">{{ game.getInfluence().toLocaleString() }}</span>
                            </div>
                        </div>
                        <div class="col">
                            <div class="row justify-content-center">
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':currentPage == 'fleets' }" style="width:80px;" @click="showFleetsPage()">
                                    <div class="h5"><i class="fas fa-fw fa-space-shuttle"></i></div>
                                    <div class="small">Fleets</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':currentPage == 'map' }" style="width:80px;" @click="showMapPage()">
                                    <div class="h5"><i class="fas fa-fw fa-map"></i></div>
                                    <div class="small">Map</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':currentPage == 'tech' }" style="width:80px;" @click="showResearchPage()">
                                    <div class="h5"><i class="fas fa-fw fa-atom"></i></div>
                                    <div class="small">Research</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':currentPage == 'overview' }" style="width:80px;" @click="showOverviewPage()">
                                    <div class="h5"><i class="fas fa-fw fa-globe"></i></div>
                                    <div class="small">Overview</div>
                                </button>
                            </div>
                        </div>
                        <div class="col-auto px-3" style="width:275px;">
                            <div class="row gx-2 align-items-center justify-content-end">
                                <button v-if="game.paused == false" type="button" class="col-auto btn lh-1" @click="togglePause()">
                                    <div class="h6"><i class="fas fa-fw fa-pause"></i></div>
                                    <div class="small">Game<br>Resumed</div>
                                </button>
                                <button v-if="game.paused == true" type="button" class="col-auto btn lh-1" @click="togglePause()">
                                    <div class="h6"><i class="fas fa-fw fa-play"></i></div>
                                    <div class="small">Game<br>Paused</div>
                                </button>
                                <div class="col-auto text-start pe-2">
                                    <div class="lh-1">
                                        <span class="medium text-normal">Year</span>
                                        <span class="medium text-white">{{ years }}</span>
                                    </div>
                                    <div class="lh-1">
                                        <span class="medium text-normal">Day</span>
                                        <span class="medium text-white">{{ days }}</span>
                                    </div>
                                </div>
                                <div class="col-auto text-end ps-2">
                                    <div class="medium text-normal">Bonus Time</div>
                                    <div class="medium text-white"><FormatTime :value="game.bonus.count / 1000" /></div>
                                </div>
                                <button v-if="game.bonusActive == false" type="button" class="col-auto btn lh-1" :class="{ 'disabled':game.bonus.count <= 0 }" @click="toggleBonus()">
                                    <div class="h6"><i class="fas fa-fw fa-play"></i></div>
                                    <div class="small">Bonus<br>Disabled</div>
                                </button>
                                <button v-if="game.bonusActive == true" type="button" class="col-auto btn lh-1" @click="toggleBonus()">
                                    <div class="h6 text-success"><i class="fas fa-fw fa-pause"></i></div>
                                    <div class="small text-success">Bonus<br>Enabled</div>
                                </button>
                            </div>
                        </div>
                    </div>
                    
                    <div class="position-absolute bottom-0 start-0 end-0 border-top bg-1 d-flex align-items-center" style="height:50px;">
                        <div class="col-auto px-3" style="width:275px;">
                            <div class="row gx-2 align-items-baseline">
                                <span class="col-auto text-normal">Research Points</span>
                                <span class="col-auto" :class="{ 'text-white':game.researchPoint.count > 0, 'text-gray':game.researchPoint.count == 0 }"><FormatNumber :value="game.researchPoint.count" /></span>
                                <span class="col-auto small" :class="{ 'text-success':game.researchPoint.prod > 0, 'text-gray':game.researchPoint.prod == 0 }"><small class="opacity-75">+</small><FormatNumber :value="game.researchPoint.prod" /> <small class="opacity-75">/s</small></span>
                            </div>
                        </div>
                        <div class="col">
                            <div v-if="currentPage == 'map'" class="row align-items-center justify-content-center">
                                <button v-if="false" type="button" class="col-auto btn rounded-0 lh-1" @click="nebulaPrevious()">
                                    <i class="fas fa-fw fa-chevron-left"></i>
                                </button>
                                <div class="col-auto text-center" style="width:250px;">
                                    <span class="h5 text-normal">{{ $t('nebulaName_' + currentNebula.ref.id) }}</span>
                                </div>
                                <button v-if="false" type="button" class="col-auto btn rounded-0 lh-1" @click="nebulaNext()">
                                    <i class="fas fa-fw fa-chevron-right"></i>
                                </button>
                            </div>
                            <div v-if="currentPage != 'map' && currentPlanet.ref.civId != 'human'" class="row justify-content-center">
                            </div>
                            <div v-if="currentPage != 'map' && currentPlanet.ref.civId == 'human'" class="row justify-content-center">
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'extraction' }" style="width:75px;" @click="showExtractionPage()">
                                    <div class="h5"><i class="fas fa-fw fa-hard-hat"></i></div>
                                    <div class="small">Extraction</div>
                                </button>
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'production' }" style="width:75px;" @click="showProductionPage()">
                                    <div class="h5"><i class="fas fa-fw fa-industry"></i></div>
                                    <div class="small">Production</div>
                                </button>
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'energy' }" style="width:75px;" @click="showEnergyPage()">
                                    <div class="h5"><i class="fas fa-fw fa-bolt"></i></div>
                                    <div class="small">Energy</div>
                                </button>
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'labs' }" style="width:75px;" @click="showLabsPage()">
                                    <div class="h5"><i class="fas fa-fw fa-flask"></i></div>
                                    <div class="small">Labs</div>
                                </button>
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'others' }" style="width:75px;" @click="showOthersPage()">
                                    <div class="h5"><i class="fas fa-fw fa-building"></i></div>
                                    <div class="small">Others</div>
                                </button>
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'shipyard' }" style="width:75px;" @click="showShipyardPage()">
                                    <div class="h5"><i class="fas fa-fw fa-rocket"></i></div>
                                    <div class="small">Shipyard</div>
                                </button>
                            </div>
                        </div>					
                        <div class="col-auto px-3" style="width:275px;">
                            <div class="row justify-content-end">
                                <button type="button" class="col-auto btn lh-1" style="width:66px;" @click="manualSave()">
                                    <div class="h5"><i class="fas fa-fw fa-save"></i></div>
                                    <div class="small">Save</div>
                                </button>
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'settings' }" style="width:66px;" @click="showSettingsPage()">
                                    <div class="h5"><i class="fas fa-fw fa-cogs"></i></div>
                                    <div class="small">Settings</div>
                                </button>
                                <button type="button" class="col-auto btn lh-1" style="width:66px;" @click="showTutorial()">
                                    <div class="h5"><i class="fas fa-fw fa-question-circle"></i></div>
                                    <div class="small">Tutorial</div>
                                </button>
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'fleets'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-2">
                                    <div class="col-12">
                                        <button type="button" class="w-100 btn" :class="{ 'bg-2 text-white':currentFleetsMenu == 'ground' }" @click="currentFleetsMenu = 'ground'">
                                            <span class="h6">Ground Ships</span>
                                        </button>
                                    </div>
                                    <div class="col-12">
                                        <button type="button" class="w-100 btn" :class="{ 'bg-2 text-white':currentFleetsMenu == 'orbitting' }" @click="currentFleetsMenu = 'orbitting'">
                                            <span class="h6">Orbitting Fleets</span>
                                        </button>
                                    </div>
                                    <div class="col-12">
                                        <button type="button" class="w-100 btn" :class="{ 'bg-2 text-white':currentFleetsMenu == 'travelling' }" @click="currentFleetsMenu = 'travelling'">
                                            <span class="h6">Travelling Fleets</span>
                                        </button>
                                    </div>
                                </div>
                            </div>
                            <div class="col d-flex flex-column scrollbar" style="overflow-y:auto;">
                                <div v-if="currentFleetsMenu == 'ground'" class="h-100 col p-3">
                                    <FleetsGroundSummary v-for="planet in game.getHumanPlanets()" :key="planet.id" :planet="planet" />
                                </div>
                                <div v-if="currentFleetsMenu == 'orbitting'" class="h-100 col p-3">
                                    <div v-if="game.getOrbittingFleets().length < 1" class="text-center">
                                        <span class="text-gray">There is no orbitting fleet to show</span>
                                    </div>
                                    <FleetsOrbittingSummary v-for="(fleet, index) in game.getOrbittingFleets()" :key="index" :index="index" :fleet="fleet" />
                                </div>
                                <div v-if="currentFleetsMenu == 'travelling'" class="h-100 col p-3">
                                    <div v-if="game.travels.length < 1" class="text-center">
                                        <span class="text-gray">There is no travelling fleet to show</span>
                                    </div>
                                    <TravelSummary v-for="(travel, index) in game.travels" :key="index" :index="index" :travel="travel" />
                                </div>
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <FleetsGroundDetails v-if="currentFleetsGround" :planet="currentFleetsGround" />
                                <FleetsOrbittingDetails v-if="currentFleetsOrbitting" :fleet="currentFleetsOrbitting" />
                                <TravelDetails v-if="currentTravel" :travel="currentTravel" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'map'" class="page">
                        <div class="h-100 d-flex align-items-stretch scrollbar" style="overflow-y:auto;">
                            <div class="h-100 col p-3 d-flex align-items-center justify-content-center" :class="{ 'bg-perseus':currentNebula.ref.id == 'perseus', 'bg-andromeda':currentNebula.ref.id == 'andromeda', 'bg-void':currentNebula.ref.id == 'void' }" style="overflow-y:auto;">
                                <div class="position-relative" style="width:1024px; height:588px;">
                                    <MapRoute v-for="(route, index) in currentNebula.getVisibleRoutes()" :key="'route' + index" :route="route" />
                                    <MapPlanet v-for="planet in currentNebula.getVisiblePlanets()" :key="planet.id" :planet="planet" />
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'research'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <div class="row g-1">
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <ResearchSummary :tech="game.techs['astronomy']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <i class="text-normal fas fa-fw fa-chevron-right"></i>
                                            </div>
                                            <div class="col">
                                                <ResearchSummary :tech="game.techs['science']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col"></div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <ResearchSummary :tech="game.techs['mineralogy']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <i v-if="game.techs['vulcan'].isVisible()" class="text-normal fas fa-fw fa-chevron-right"></i>
                                            </div>
                                            <div class="col">
                                                <ResearchSummary v-if="game.techs['vulcan'].isVisible()" :tech="game.techs['vulcan']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col text-center">
                                                <div v-if="game.techs['military'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['military'].isVisible()" :tech="game.techs['military']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="game.techs['intelligence'].isVisible()" :tech="game.techs['intelligence']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="game.techs['intelligence'].isVisible()" class="text-normal fas fa-fw fa-chevron-left"></i>
                                            </div>
                                            <div class="col">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="game.techs['electronic'].isVisible()" :tech="game.techs['electronic']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="game.techs['electronic'].isVisible()" class="text-normal fas fa-fw fa-chevron-left"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="game.techs['material'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['material'].isVisible()" :tech="game.techs['material']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="game.techs['ice'].isVisible()" class="text-normal fas fa-fw fa-chevron-right"></i>
                                            </div>
                                            <div class="col">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="game.techs['ice'].isVisible()" :tech="game.techs['ice']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <div v-if="game.techs['artofwar'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['artofwar'].isVisible()" :tech="game.techs['artofwar']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['halean'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['halean'].isVisible()" :tech="game.techs['halean']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['nuclear'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['nuclear'].isVisible()" :tech="game.techs['nuclear']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['chemical'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['chemical'].isVisible()" :tech="game.techs['chemical']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="game.techs['hydro'].isVisible()" class="text-normal fas fa-fw fa-chevron-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="game.techs['hydro'].isVisible()" class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="game.techs['hydro'].isVisible()" :tech="game.techs['hydro']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <div v-if="game.techs['karanartofwar'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['karanartofwar'].isVisible()" :tech="game.techs['karanartofwar']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['quantum'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['quantum'].isVisible()" :tech="game.techs['quantum']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="game.techs['secret'].isVisible()" class="text-normal fas fa-fw fa-chevron-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="game.techs['secret'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['secret'].isVisible()" :tech="game.techs['secret']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="game.techs['secret'].isVisible()" class="text-normal fas fa-fw fa-chevron-left"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="game.techs['rhodium'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['rhodium'].isVisible()" :tech="game.techs['rhodium']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['ammonia'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['ammonia'].isVisible()" :tech="game.techs['ammonia']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <div v-if="game.techs['xiranartofwar'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['xiranartofwar'].isVisible()" :tech="game.techs['xiranartofwar']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['karannuclear'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['karannuclear'].isVisible()" :tech="game.techs['karannuclear']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['spacemining'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['spacemining'].isVisible()" :tech="game.techs['spacemining']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="game.techs['osmium'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['osmium'].isVisible()" :tech="game.techs['osmium']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col"></div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                             <div class="col">
                                                <div v-if="game.techs['protohalean'].isVisible()" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-down"></i></div>
                                                <ResearchSummary v-if="game.techs['protohalean'].isVisible()" :tech="game.techs['protohalean']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="game.techs['darkmatter'].isVisible()" class="text-normal fas fa-fw fa-chevron-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="game.techs['darkmatter'].isVisible()" class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="game.techs['darkmatter'].isVisible()" :tech="game.techs['darkmatter']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col"></div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col"></div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <ResearchDetails v-if="currentResearch" :tech="currentResearch" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'overview'" class="page pt-3 px-2">
                        <div class="h-100 row g-3 scrollbar" style="overflow-y:auto;">
                            <div class="col-12">
                                <div class="row g-1 align-items-center justify-content-center">
                                    <div v-for="(planet, key) of game.getHumanPlanets()" :key="key" class="col-3">
                                        <button type="button" class="w-100 btn bg-1 rounded border" @click="showPlanetPage(planet)">
                                            <div class="row g-2 align-items-center justify-content-center">
                                                <div class="col-auto">
                                                    <img :src="require(`~/assets/planets/${key}.png`)" width="24px" />
                                                </div>
                                                <div class="col-auto">
                                                    <span>{{ $t('planetName_' + key) }}</span>
                                                </div>
                                            </div>
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'planet'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <div class="col-12 text-center">
                                        <div class="h5 text-normal">{{ $t('planetName_' + currentPlanet.ref.id) }}</div>
                                    </div>
                                    <PlanetInfo :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy v-if="currentPlanet.ref.civId == 'human'" :planet="currentPlanet" class="col-12" />
                                    <div class="col-12">
                                        <div v-for="(prod, key) of currentPlanet.ref.prod" :key="key" class="row gx-2">
                                            <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                                            <span class="col-auto" :class="{ 'text-white':prod == 1, 'text-danger':prod < 1, 'text-success':prod > 1 }"><small class="opacity-75">x</small> {{ prod.toFixed(2) }}</span>
                                        </div>
                                    </div>
                                </div>
                            </div>               
                            <div class="col position-relative d-flex flex-column scrollbar" style="overflow-y:auto;">
                                <div class="position-absolute w-100 pt-3 px-3">
                                    <div class="text-center small text-gray">Controlled by</div>
                                    <div class="text-center">
                                        <span v-if="currentPlanet.ref.civId != null" class="h5" :class="{ 'text-normal':currentPlanet.ref.civId == 'human', 'text-danger':currentPlanet.ref.civId != null && currentPlanet.ref.civId != 'human', 'text-gray':currentPlanet.ref.civId == null }">{{ $t('civName_' + currentPlanet.ref.civId) }}</span>
                                        <span v-if="currentPlanet.ref.civId == null" class="h5 text-gray">No One</span>
                                    </div>
                                </div>
                                <div :class="'flex-fill pt-3 d-flex align-items-center bg-' + currentPlanet.ref.id">
                                    <button v-if="currentPlanet.ref.civId == 'human' && Object.keys(game.getHumanPlanets()).length > 1" type="button" id="arrow_left" class="btn" @click="currentPlanetPrevious()">
                                        <i class="fas fa-fw fa-chevron-left"></i>
                                    </button>
                                    <div class="col"></div>
                                    <button v-if="currentPlanet.ref.civId == 'human' && Object.keys(game.getHumanPlanets()).length > 1" type="button" id="arrow_right" class="btn" @click="currentPlanetNext()">
                                        <i class="fas fa-fw fa-chevron-right"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div v-if="currentPlanet.ref.civId == 'human'" class="row g-3">
                                    <div class="col-12 text-center">
                                        <span class="h5 text-primary">Status</span>
                                    </div>
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                                <div v-if="currentPlanet.ref.civId != 'human'" class="row g-4">
                                    <div v-if="currentPlanet.unlockTechId != null" class="col-12">
                                        <div class="text-center mb-1">
                                            <span class="h5 text-primary">Colonize  to unlock</span>
                                        </div>
                                        <div class="text-center">
                                            <span class="text-white">{{ $t('techName_' + currentPlanet.unlockTechId) }}</span>
                                        </div>
                                    </div>
                                    <div v-if="currentPlanet.fleets.length > 0" class="col-12">
                                        <div class="text-center mb-3">
                                            <span class="h5 text-primary">Orbitting Fleets</span>
                                        </div>
                                        <div class="row g-3">
                                            <div v-for="(fleet, index) in currentPlanet.fleets" :key="index" class="col-12">
                                                <div class="mb-2">
                                                    <span class="h6 text-gray">{{ fleet.name }}</span>
                                                </div>
                                                <div class="row gx-2">
                                                    <div class="col"><span class="text-normal">Civilization</span></div>
                                                    <div class="col-auto"><span :class="{ 'text-white':fleet.civId == 'human', 'text-danger':fleet.civId != 'human' }">{{ $t('civName_' + fleet.civId) }}</span></div>
                                                </div>
                                                <div class="row gx-2">
                                                    <div class="col"><span class="text-normal">Experience</span></div>
                                                    <div class="col-auto"><span class="text-white">{{ fleet.experience }}</span></div>
                                                </div>
                                                <div class="row gx-2">
                                                    <div class="col"><span class="text-normal">Military Value</span></div>
                                                    <div class="col-auto"><span class="text-white"><FormatNumber :value="fleet.getMilitaryValue()" /></span></div>
                                                </div>
                                                <div class="row gx-2">
                                                    <div class="col"><span class="text-normal">Total Power</span></div>
                                                    <div class="col-auto"><span class="text-white"><FormatNumber :value="fleet.getPower()" /></span></div>
                                                </div>
                                                <div class="row gx-2 mb-2">
                                                    <div class="col"><span class="text-normal">Total HP</span></div>
                                                    <div class="col-auto"><span class="text-white"><FormatNumber :value="fleet.getHP()" /></span></div>
                                                </div>
                                                <div v-for="(count, sId) in fleet.getShips()" :key="sId" class="row gx-2">
                                                    <div class="col"><span class="text-normal">{{ $t('shipName_' + sId) }}</span></div>
                                                    <div class="col-auto"><span class="text-white"><FormatNumber :value="count" /></span></div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="currentPage == 'extraction'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <BuildingSummary v-for="(building, key) of currentPlanet.getUnlockedBuildingsByType('extraction')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'production'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <BuildingSummary v-for="(building, key) of currentPlanet.getUnlockedBuildingsByType('prod')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'energy'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <BuildingSummary v-for="(building, key) of currentPlanet.getUnlockedBuildingsByType('energy')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'labs'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <BuildingSummary v-for="(building, key) of currentPlanet.getUnlockedBuildingsByType('research')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'others'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <div v-if="Object.keys(currentPlanet.getUnlockedBuildingsByType('other')).length < 1" class="text-center"><span class="text-gray">There is no building to show</span></div>
                                <BuildingSummary v-for="(building, key) of currentPlanet.getUnlockedBuildingsByType('other')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>

                    <div v-if="currentPage == 'shipyard'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <div v-if="Object.keys(currentPlanet.getVisibleShips()).length < 1" class="text-center"><span class="text-gray">There is no ship to show</span></div>
                                <ShipSummary v-for="(ship, key) of currentPlanet.getVisibleShips()" :key="key" :ship="ship" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <ShipDetails v-if="currentShip" :ship="currentShip" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'settings'" class="page pt-4 px-3">
                        <div class="h-100 row g-4 justify-content-center scrollbar" style="overflow-y:auto;">
                            <div class="col-5 mb-4">
                                <div class="row g-2">
                                    <div class="col-12 text-center h5 text-primary">About</div>
                                    <div class="col-12 text-center text-normal">This is a rewriting/remake of the original game created by <span class="text-white">Cheslava</span><br><a target="_blank" href="https://www.heartofgalaxy.com" class="text-white">heartofgalaxy.com</a></div>
                                    <div class="col-12 text-center text-danger">This is still under development with bugs and maybe data lost!</div>
                                    <div class="col-12 text-center text-normal mb-2">To support the dev and to stay informed</div>
                                    <div class="col-3">
                                        <a href="https://www.patreon.com/bePatron?u=61283131" target="_blank" class="btn bg-bar border" style="width:78px;">
                                            <img :src="require(`~/assets/ui/patreon.png`)" width="24px" height="24px" />                                    
                                            <div class="small lh-sm mt-2">Become a supporter</div>
                                        </a>
                                    </div>
                                    <div class="col-3">
                                        <a href="https://ko-fi.com/freddecgames" target="_blank" class="btn bg-bar border" style="width:78px;">
                                            <img :src="require(`~/assets/ui/kofi.png`)" width="24px" height="24px" />
                                            <div class="small lh-sm mt-2">Buy me a Ko-fi</div>
                                        </a>
                                    </div>
                                    <form class="col-3" action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_blank">
                                        <input type="hidden" name="cmd" value="_s-xclick">
                                        <input type="hidden" name="hosted_button_id" value="7XYD7SJFKQ8M4">
                                        <button type="submit" class="btn bg-bar border" style="width:78px;">
                                            <img :src="require(`~/assets/ui/paypal.png`)" width="24px" height="24px" />
                                            <div class="small lh-sm mt-2">Make a donation</div>
                                        </button>
                                    </form>
                                    <div class="col-3">
                                        <a href="https://discord.gg/3UkgeeT9CV" target="_blank" class="btn bg-bar border" style="width:78px;">
                                            <img :src="require(`~/assets/ui/discord.png`)" width="24px" height="24px" alt="Discord" />
                                            <div class="small lh-sm mt-2">News and information</div>
                                        </a>
                                    </div>
                                </div>
                            </div>
                            <div class="col-5 mb-4">
                                <div class="row g-2 justify-content-center">
                                    <div class="col-12 text-center h5 text-danger">Hard reset</div>
                                    <div class="col-auto">
                                        <button type="button" class="btn bg-bar border text-danger" style="width:85px;" @click="showHardResetPopup()">
                                            <div class="text-center h5"><i class="fas fa-fw fa-skull"></i></div>
                                            <div class="small lh-sm mt-2">Wipe Local Data</div>
                                        </button>
                                    </div>
                                </div>
                            </div>
                            <div class="col-5 mb-4">
                                <div class="row g-2 justify-content-center">
                                    <div class="col-12 text-center h5 text-primary">Export</div>
                                    <div class="col-12">
                                        <textarea spellcheck="false" rows="5" class="w-100 rounded bg-1 border text-normal p-2">{{ exportGameData }}</textarea>                                        
                                    </div>
                                </div>
                            </div>
                            <div class="col-5 mb-4">
                                <div class="row g-2 justify-content-center">
                                    <div class="col-12 text-center h5 text-primary">Import</div>
                                    <div class="col-12">
                                        <textarea spellcheck="false" rows="5" class="w-100 rounded bg-1 border text-normal p-2" v-model="importExportData"></textarea>
                                    </div>
                                    <div class="col-12 mt-2">
                                        <div class="row gx-2 align-items-center">
                                            <div class="col text-warning small"><i class="fas fa-fw fa-exclamation-triangle"></i> Import save from original game is not supported for the moment</div>
                                            <button type="button" class="col-auto btn bg-bg-1 border" @click="importGameData()">Import Save</button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
            
        </div>
    
    </div>
</template>

<script>
var resourceRefs = [
    
    {	id: "ammonia",	techReqs: { ammonia: 1 },	},
    {	id: "ammunition",	techReqs: { military: 1 },	},
    {	id: "antimatter",	techReqs: { quantum: 1 },	},
    {	id: "armor",	techReqs: { military: 12 },	},
    {	id: "caesium",	techReqs: { karannuclear: 1 },	},
    {	id: "circuit",	techReqs: { electronic: 1 },	},
    {	id: "coolant",	techReqs: { ice: 10 },	},
    {	id: "darkmatter",	techReqs: { darkmatter: 1 },	},
    {	id: "emptybattery",	techReqs: { electronic: 8 },	},
    {	id: "engine",	techReqs: { military: 16 },	},
    {	id: "fuel",	techReqs: null,	},
    {	id: "fullbattery",	techReqs: { electronic: 8 },	},
    {	id: "graphite",	techReqs: null,	},
    {	id: "hydrogen",	techReqs: { nuclear: 1 },	},
    {	id: "ice",	techReqs: { ice: 1 },	},
    {	id: "iron",	techReqs: null,	},
    {	id: "meissnercell", 	techReqs: { electronic: 35 },	},
    {	id: "meissnerium",	techReqs: { material: 35 },	},
    {	id: "methane",	techReqs: null,	},
    {	id: "mkembryo",	techReqs: { osmium: 1 },	},
    {	id: "nanotube",	techReqs: { chemical: 30 },	},
    {	id: "oil",	techReqs: { chemical: 1 },	},
    {	id: "osmium",	techReqs: { osmium: 1 },	},
    {	id: "plastic",	techReqs: { material: 8 },	},
    {	id: "qaser",	techReqs: { protohalean: 1 },	},
    {	id: "rhodium",	techReqs: { hydro: 1 },	},
    {	id: "robot",	techReqs: { intelligence: 1 },	},
    {	id: "sand",	techReqs: { mineralogy: 4 },	},
    {	id: "silicon",	techReqs: { electronic: 1 },	},
    {	id: "steel",	techReqs: null,	},
    {	id: "sulfur",	techReqs: { vulcan: 1 },	},
    {	id: "superconductor",	techReqs: { electronic: 30 },	},
    {	id: "tammunition",	techReqs: { artofwar: 1 },	},
    {	id: "technetium",	techReqs: { halean: 1 },	},
    {	id: "thorium",	techReqs: { karannuclear: 1 },	},
    {	id: "titanium",	techReqs: { mineralogy: 4 },	},
    {	id: "uammunition",	techReqs: { military: 8 },	},
    {	id: "uranium",	techReqs: { mineralogy: 4 },	},
    {	id: "water",	techReqs: { hydro: 1 },	},
]

var buildingRefs = [

    {	id: "mine",	type: "extraction",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { iron: 2 },	cost: { iron: 10, steel: 9.75e-4, titanium: 2.1e-12 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.5 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "methaneExtractor",	type: "extraction",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { methane: 1 },	cost: { iron: 100, steel: .1, titanium: 3e-5 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.5 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "graphiteExtractor",	type: "extraction",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic", "radioactive", "acid"],	prod: { graphite: 1 },	cost: { iron: 500, steel: .5, titanium: 4e-4 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.5 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "pumpjack",	type: "extraction",	techReqs: { chemical: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { oil: 1 },	cost: { steel: 1e3, titanium: .9 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "metalCollector",	type: "extraction",	techReqs: { mineralogy: 3 },	envs: ["desert", "ice", "terrestrial", "metallic", "radioactive", "acid"],	prod: { titanium: 10, uranium: 1 },	cost: { steel: 2e3, titanium: .9 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -50,	researchPoint: 0,	desc: false,	},
    {	id: "waterPump",	type: "extraction",	techReqs: { hydro: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { water: 1 },	cost: { steel: 1e4, titanium: 1e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -10,	researchPoint: 0,	desc: false,	},
    {	id: "sandQuarry",	type: "extraction",	techReqs: { mineralogy: 4 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { sand: 1 },	cost: { steel: 5e3, titanium: 1e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -80,	researchPoint: 0,	desc: false,	},
    {	id: "iceDrill",	type: "extraction",	techReqs: { ice: 1 },	envs: ["ice"],	prod: { ice: 1 },	cost: { iron: 1e4, steel: 2e3, titanium: 1e3 },	mult: { iron: 1.1, steel: 1.2, titanium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "pumpingPlatform",	type: "extraction",	techReqs: null,	envs: ["ocean"],	prod: { water: 5 },	cost: { steel: 5e3, titanium: 1e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "submergedMetalMine",	type: "extraction",	techReqs: { hydro: 1 },	envs: ["ocean", "ammonia"],	prod: { iron: 5, titanium: 3, uranium: 1 },	cost: { iron: 5e3, steel: 1e3, titanium: 100 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "submergedSandMine",	type: "extraction",	techReqs: { hydro: 1 },	envs: ["ocean", "ammonia"],	prod: { graphite: 2, sand: 1, rhodium: 1, osmium: 1 },	cost: { iron: 5e3, steel: 1e3, titanium: 100 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "rhodiumExtractor",	type: "extraction",	techReqs: { rhodium: 1 },	envs: ["desert", "ice", "lava", "metallic", "radioactive", "acid"],	prod: { titanium: 20, rhodium: 2 },	cost: { silicon: 25e4, sand: 1e8, fuel: 1e8 },	mult: { silicon: 1.3, sand: 1.4, fuel: 1.5 },	buildable: true,	energy: -800,	researchPoint: 0,	desc: false,	},
    {	id: "thoriumExtractor",	type: "extraction",	techReqs: { karannuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { thorium: 8, caesium: 1 },	cost: { titanium: 5e7, nanotube: 5e5, engine: 3e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: -300,	researchPoint: 0,	desc: false,	},
    {	id: "methaneHarvester",	type: "extraction",	techReqs: null,	envs: ["gas"],	prod: { methane: 1 },	cost: { iron: 1e4, steel: 100, plastic: 10, titanium: 2.1e-12 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "hydrogenCondenser",	type: "extraction",	techReqs: { nuclear: 1 },	envs: ["gas"],	prod: { hydrogen: 10 },	cost: { steel: 25e3, titanium: 1e4, plastic: 500 },	mult: { steel: 1.2, titanium: 1.3, plastic: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "sandMine",	type: "extraction",	techReqs: null,	envs: ["desert"],	prod: { sand: 1 },	cost: { iron: 35e4 },	mult: { iron: 1.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "ammoniaExtractor",	type: "extraction",	techReqs: { ammonia: 1 },	envs: ["ammonia"],	prod: { ammonia: 1 },	cost: { nanotube: 1e6, engine: 1e3 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "osmiumExtractor",	type: "extraction",	techReqs: { osmium: 1 },	envs: ["ice", "metallic", "radioactive"],	prod: { osmium: 1 },	cost: { silicon: 5e5, rhodium: 3.5e3 },	mult: { silicon: 1.3, rhodium: 1.5 },	buildable: true,	energy: -1200,	researchPoint: 0,	desc: false,	},
    {	id: "sulfurMine",	type: "extraction",	techReqs: { vulcan: 1 },	envs: ["lava", "radioactive", "acid"],	prod: { sulfur: 3, graphite: 2 },	cost: { technetium: 1e5, graphite: 1e6 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: false,	},
    {	id: "lavaMine",	type: "extraction",	techReqs: { vulcan: 1 },	envs: ["lava", "acid"],	prod: { titanium: 8 },	cost: { technetium: 1e5, graphite: 1e6 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: false,	},
    
    {	id: "darkmatterRefiner",	type: "prod",	techReqs: { darkmatter: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { antimatter: -100, meissnercell: -10, darkmatter: 1 },	cost: { qaser: 1e4, meissnerium: 5e5 },	mult: { qaser: 1.2, meissnerium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "ammoniaElectrolyzer",	type: "prod",	techReqs: { ammonia: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { ammonia: -10, hydrogen: 30 },	cost: { nanotube: 1e6, engine: 5e3 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: false,	},
    {	id: "methaneProcesser",	type: "prod",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { methane: -2, fuel: 1 },	cost: { iron: 100, steel: .25, titanium: 2e-4 },	mult: { iron: 1.1, steel: 1.2, titanium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "foundry",	type: "prod",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { graphite: -1, iron: -2, fuel: -2, steel: 2 },	cost: { iron: 1e3, steel: .48, titanium: .01 },	mult: { iron: 1.1, steel: 1.2, titanium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "oilRefinery",	type: "prod",	techReqs: { chemical: 2 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { oil: -1, fuel: 5 },	cost: { steel: 5e3, titanium: 100 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "plasticFactory",	type: "prod",	techReqs: { material: 8 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { oil: -3, plastic: 1 },	cost: { steel: 1e4, titanium: 5e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: false,	},
    {	id: "sandSmelter",	type: "prod",	techReqs: { electronic: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { sand: -1, silicon: 1 },	cost: { steel: 2e4, titanium: 1e4, plastic: 500 },	mult: { steel: 1.1, titanium: 1.2, plastic: 1.3 },	buildable: true,	energy: -50,	researchPoint: 0,	desc: false,	},
    {	id: "electronicFacility",	type: "prod",	techReqs: { electronic: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { plastic: -2, silicon: -10, circuit: 1 },	cost: { steel: 1e5, titanium: 5e4, plastic: 2e3 },	mult: { steel: 1.2, titanium: 1.3, plastic: 1.4 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: false,	},
    {	id: "ammunitionFactory",	type: "prod",	techReqs: { military: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { steel: -100, fuel: -5, plastic: -3, ammunition: 10 },	cost: { titanium: 1e5, plastic: 2e4 },	mult: { titanium: 1.25, plastic: 1.35 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: true,	},
    {	id: "waterFreezer",	type: "prod",	techReqs: { ice: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { water: -2, ice: 3 },	cost: { iron: 28e3, steel: 1e4, titanium: 500 },	mult: { iron: 1.15, steel: 1.27, titanium: 1.35 },	buildable: true,	energy: -30,	researchPoint: 0,	desc: false,	},
    {	id: "nanotubeFactory",	type: "prod",	techReqs: { material: 15 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { graphite: -200, plastic: -10, hydrogen: -100, nanotube: 5 },	cost: { titanium: 25e4, plastic: 5e4, circuit: 15e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "coolantFactory",	type: "prod",	techReqs: { ice: 10 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { ice: -2e3, methane: -100, coolant: 8 },	cost: { plastic: 3e5, circuit: 1e5 },	mult: { plastic: 1.25, circuit: 1.35 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "robotFactory",	type: "prod",	techReqs: { intelligence: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { coolant: -8, circuit: -50, nanotube: -28, steel: -800, fullbattery: -100, robot: 1 },	cost: { plastic: 5e5, circuit: 25e4, nanotube: 5e4 },	mult: { plastic: 1.2, circuit: 1.3, nanotube: 1.4 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "armorFactory",	type: "prod",	techReqs: { military: 12 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { steel: -1e3, titanium: -500, plastic: -200, armor: 3 },	cost: { circuit: 5e5, nanotube: 1e5 },	mult: { circuit: 1.25, nanotube: 1.35 },	buildable: true,	energy: -2e3,	researchPoint: 0,	desc: true,	},
    {	id: "engineFactory",	type: "prod",	techReqs: { military: 16 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { oil: -500, coolant: -100, circuit: -100, steel: -1e3, titanium: -500, plastic: -200, engine: 2 },	cost: { circuit: 8e5, nanotube: 2e5, robot: 1e3 },	mult: { circuit: 1.2, nanotube: 1.3, robot: 1.4 },	buildable: true,	energy: -2500,	researchPoint: 0,	desc: true,	},
    {	id: "iceMelter",	type: "prod",	techReqs: { ice: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "ammonia"],	prod: { fuel: -1, ice: -3, water: 2 },	cost: { iron: 28e3, steel: 1e4, titanium: 500 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "batteryFactory",	type: "prod",	techReqs: { electronic: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { hydrogen: -1e3, steel: -35e4, emptybattery: 1e3 },	cost: { titanium: 15e4, plastic: 35e3, circuit: 2e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: true,	},
    {	id: "electrolyzer",	type: "prod",	techReqs: { hydro: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { water: -1, hydrogen: 2 },	cost: { titanium: 25e3, plastic: 1e3, circuit: 100 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -50,	researchPoint: 0,	desc: false,	},
    {	id: "uraniumShellAssembler",	type: "prod",	techReqs: { military: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { ammunition: -15, uranium: -8, uammunition: 5 },	cost: { circuit: 3e4, plastic: 5e4 },	mult: { circuit: 1.35, plastic: 1.25 },	buildable: true,	energy: -300,	researchPoint: 0,	desc: true,	},
    {	id: "algaeOilFarm",	type: "prod",	techReqs: null,	envs: ["ocean"],	prod: { water: -7, oil: 3 },	cost: { iron: 28e3, steel: 1e4, titanium: 500 },	mult: { iron: 1.15, steel: 1.27, titanium: 1.35 },	buildable: true,	energy: -10,	researchPoint: 0,	desc: false,	},
    {	id: "submergedOilRefinery",	type: "prod",	techReqs: null,	envs: ["ocean", "ammonia"],	prod: { oil: -3, fuel: 10 },	cost: { steel: 15e3, titanium: 1e3 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "nanotubeDome",	type: "prod",	techReqs: { material: 15 },	envs: ["ocean", "ammonia"],	prod: { graphite: -170, water: -15, plastic: -8, hydrogen: -80, nanotube: 5 },	cost: { titanium: 25e4, plastic: 5e4, circuit: 15e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "tammunitionAssembler",	type: "prod",	techReqs: { artofwar: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { uammunition: -3, technetium: -50, steel: -1e5, tammunition: 1 },	cost: { technetium: 1e5, graphite: 2e5 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -750,	researchPoint: 0,	desc: true,	},
    {	id: "particleAccelerator",	type: "prod",	techReqs: { quantum: 1 },	envs: ["gas"],	prod: { hydrogen: -1e3, antimatter: 1 },	cost: { steel: 1e8, technetium: 1e6 },	mult: { steel: 1.8, technetium: 1.25 },	buildable: true,	energy: -1e3,	researchPoint: 0,	desc: false,	},
    {	id: "rhodiumSandSmelter",	type: "prod",	techReqs: { rhodium: 1 },	envs: ["lava", "acid", "desert", "metallic"],	prod: { rhodium: -5, sand: -150, silicon: 100 },	cost: { silicon: 25e4, sand: 1e8, fuel: 1e8 },	mult: { silicon: 1.3, sand: 1.4, fuel: 1.5 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: false,	},
    {	id: "floatingFuelConverter",	type: "prod",	techReqs: null,	envs: ["gas"],	prod: { methane: -5, fuel: 3 },	cost: { steel: 1e4, titanium: 8e3, plastic: 5e3 },	mult: { steel: 1.2, titanium: 1.3, plastic: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "polymerizer",	type: "prod",	techReqs: { material: 8 },	envs: ["gas"],	prod: { methane: -80, water: -5, plastic: 2 },	cost: { titanium: 2e4, plastic: 1e3 },	mult: { titanium: 1.25, plastic: 1.35 },	buildable: true,	energy: -150,	researchPoint: 0,	desc: false,	},
    {	id: "metallokoptaClonator",	type: "prod",	techReqs: { osmium: 1 },	envs: ["ice", "desert", "metallic", "radioactive"],	prod: { osmium: -5, robot: -5, sulfur: -20, silicon: -85, mkembryo: 1 },	cost: { silicon: 25e5, rhodium: 15e3 },	mult: { silicon: 1.3, rhodium: 1.5 },	buildable: true,	energy: -2e3,	researchPoint: 0,	desc: false,	},
    {	id: "ammoniaRefrigerator",	type: "prod",	techReqs: { ammonia: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { ammonia: -10, methane: -200, coolant: 8 },	cost: { nanotube: 1e7, engine: 1e6 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "methaneAggregator",	type: "prod",	techReqs: { chemical: 30 },	envs: ["lava", "gas"],	prod: { methane: -1e6, nanotube: 20 },	cost: { steel: 1e9, nanotube: 10e6, engine: 1e5 },	mult: { steel: 1.35, nanotube: 1.3, engine: 1.3 },	buildable: true,	energy: -5e3,	researchPoint: 0,	desc: false,	},
    {	id: "ceramicFoundry",	type: "prod",	techReqs: { material: 35 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "carbon", "acid"],	prod: { water: -5e5, nanotube: -1e4, rhodium: -2e3, sulfur: -1e3, osmium: -500, iron: -2.5e3, meissnerium: 1 },	cost: { nanotube: 10e6, engine: 1e6 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "superconductorsFactory",	type: "prod",	techReqs: { electronic: 30 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "carbon", "acid"],	prod: { meissnerium: -1, coolant: -350, superconductor: 1 },	cost: { nanotube: 10e6, engine: 1e6, meissnerium: 1e3 },	mult: { meissnerium: 1.2, nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -5e3,	researchPoint: 0,	desc: false,	},
    {	id: "cellsFactory",	type: "prod",	techReqs: { electronic: 35 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "carbon", "acid"],	prod: { superconductor: -1, circuit: -1e7, nanotube: -1e7, meissnercell: 1 },	cost: { nanotube: 10e6, engine: 1e6, meissnerium: 1e3 },	mult: { meissnerium: 1.2, nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -1e4,	researchPoint: 0,	desc: false,	},
    {	id: "qaserAssembler",	type: "prod",	techReqs: { protohalean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { superconductor: -1, circuit: -1e7, nanotube: -1e7, water: -25e4, qaser: 1 },	cost: { nanotube: 1e6, engine: 25e4, meissnerium: 300 },	mult: { meissnerium: 1.2, nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -1e4,	researchPoint: 0,	desc: false,	},
    {	id: "technetiumFissor",	type: "prod",	techReqs: { halean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "radioactive", "ammonia"],	prod: { uranium: -12, technetium: 1 },	cost: { graphite: 5e6, nanotube: 500 },	mult: { graphite: 1.25, nanotube: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "haleanAICenter",	type: "prod",	techReqs: { halean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "radioactive", "ammonia"],	prod: { circuit: -50, technetium: -5, fullbattery: -100, robot: 1 },	cost: { circuit: 5e5, nanotube: 2e3, technetium: 100 },	mult: { circuit: 1.5, nanotube: 1.3, technetium: 1.2 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    
    {	id: "smallGenerator",	type: "energy",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { fuel: -3 },	cost: { iron: 2e3, steel: 100, titanium: .17 },	mult: { iron: 1.15, steel: 1.27, titanium: 1.35 },	buildable: true,	energy: 20,	researchPoint: 0,	desc: false,	},
    {	id: "thermalPlant",	type: "energy",	techReqs: { chemical: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { fuel: -10 },	cost: { iron: 5e3, steel: 1e3, titanium: .17 },	mult: { iron: 2.5, steel: 2.7, titanium: 1.9 },	buildable: true,	energy: 100,	researchPoint: 0,	desc: false,	},
    {	id: "solarCentral",	type: "energy",	techReqs: { electronic: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { steel: 1e4, titanium: 1e4, silicon: 1e3 },	mult: { steel: 1.1, titanium: 1.1, silicon: 1.3 },	buildable: true,	energy: 50,	researchPoint: 0,	desc: false,	},
    {	id: "nuclearPowerplant",	type: "energy",	techReqs: { nuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { uranium: -10, graphite: -3 },	cost: { titanium: 1e5, plastic: 2e4, graphite: 1e6 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 500,	researchPoint: 0,	desc: false,	},
    {	id: "fusionReactor",	type: "energy",	techReqs: { nuclear: 5 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { hydrogen: -150 },	cost: { titanium: 25e4, plastic: 1e5, circuit: 1e4 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: 1100,	researchPoint: 0,	desc: false,	},
    {	id: "batteryPowerPlant",	type: "energy",	techReqs: { electronic: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { fullbattery: -1e4, emptybattery: 9.5e3 },	cost: { titanium: 2e5, plastic: 5e4, circuit: 5e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: 240,	researchPoint: 0,	desc: false,	},
    {	id: "batteryCharger",	type: "energy",	techReqs: { electronic: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { emptybattery: -1e4, fullbattery: 1e4 },	cost: { steel: 5e5, titanium: 1e5, plastic: 2e4 },	mult: { steel: 1.2, titanium: 1.2, plastic: 1.2 },	buildable: true,	energy: -250,	researchPoint: 0,	desc: false,	},
    {	id: "antimatterCollider",	type: "energy",	techReqs: { quantum: 1 },	envs: ["gas"],	prod: { antimatter: -1, hydrogen: -1e3 },	cost: { steel: 100e6, technetium: 1e6 },	mult: { steel: 1.8, technetium: 1.25 },	buildable: true,	energy: 8e3,	researchPoint: 0,	desc: false,	},
    {	id: "nuclearPowerplant2",	type: "energy",	techReqs: { nuclear: 1 },	envs: ["radioactive"],	prod: { uranium: -10, graphite: -3 },	cost: { titanium: 1e5, plastic: 2e4, graphite: 1e6 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 2e3,	researchPoint: 0,	desc: false,	},
    {	id: "hydrothermalPlant",	type: "energy",	techReqs: null,	envs: ["ocean"],	prod: { water: -2, fuel: -10 },	cost: { steel: 1e4, titanium: 2e3 },	mult: { iron: 1.2, steel: 1.35, titanium: 1.5 },	buildable: true,	energy: 100,	researchPoint: 0,	desc: false,	},
    {	id: "bydroelectricPlant",	type: "energy",	techReqs: null,	envs: ["ocean"],	prod: { water: -10 },	cost: { steel: 15e3, titanium: 1e4, plastic: 1500 },	mult: { steel: 1.1, titanium: 1.2, plastic: 1.3 },	buildable: true,	energy: 200,	researchPoint: 0,	desc: false,	},
    {	id: "pressurizedWaterReactor",	type: "energy",	techReqs: { nuclear: 3 },	envs: ["ocean"],	prod: { uranium: -5, water: -20 },	cost: { titanium: 2e5, plastic: 1e5, graphite: 5e5 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 500,	researchPoint: 0,	desc: false,	},
    {	id: "thoriumReactor",	type: "energy",	techReqs: { karannuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { thorium: -5 },	cost: { titanium: 80e6, nanotube: 5e6, engine: 8e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: 2500,	researchPoint: 0,	desc: false,	},
    {	id: "thoriumReactor2",	type: "energy",	techReqs: { karannuclear: 1 },	envs: ["radioactive", "ocean"],	prod: { thorium: -5 },	cost: { titanium: 80e6, nanotube: 5e6, engine: 8e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: 5e3,	researchPoint: 0,	desc: false,	},
    {	id: "floatingGenerator",	type: "energy",	techReqs: null,	envs: ["gas"],	prod: { fuel: -3 },	cost: { iron: 2e3, steel: 100, titanium: .17 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 20,	researchPoint: 0,	desc: false,	},
    {	id: "floatingReactor",	type: "energy",	techReqs: { nuclear: 5 },	envs: ["gas"],	prod: { hydrogen: -100 },	cost: { plastic: 2e5, circuit: 5e4 },	mult: { plastic: 1.25, circuit: 1.35 },	buildable: true,	energy: 1e3,	researchPoint: 0,	desc: false,	},
    {	id: "pressurizedAmmoniaReactor",	type: "energy",	techReqs: { ammonia: 1 },	envs: ["ammonia"],	prod: { thorium: -5, ammonia: -20 },	cost: { titanium: 2e5, plastic: 1e5, graphite: 5e5 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 800,	researchPoint: 0,	desc: false,	},
    
    {	id: "ammoniaAirship",	type: "research",	techReqs: { ammonia: 1 },	envs: ["gas"],	prod: { ammonia: -100 },	cost: { nanotube: 25e9, coolant: 250e6, hydrogen: 5e9 },	mult: { nanotube: 1.2, coolant: 1.5, hydrogen: 1.3 },	buildable: true,	energy: -15e3,	researchPoint: 1,	desc: false,	},
    {	id: "laboratory",	type: "research",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: null,	cost: { iron: 1e3, steel: 200, titanium: .01 }, 	mult: { iron: 2, steel: 3, titanium: 4 },	buildable: true,	energy: -5,	researchPoint: 4,	desc: false,	},
    {	id: "oceanographicCenter",	type: "research",	techReqs: null,	envs: ["ocean", "ammonia"],	prod: { water: -2 },	cost: { steel: 2e4, titanium: 5e3 },	mult: { steel: 1.5, titanium: 2 },	buildable: true,	energy: -50, 	researchPoint: 10,	desc: false,	},
    {	id: "cryogenicLaboratory",	type: "research",	techReqs: { ice: 12 },	envs: ["ice", "radioactive"],	prod: { ice: -100 },	cost: { plastic: 3e5, circuit: 1e5 },	mult: { plastic: 1.25, circuit: 1.35 },	buildable: true,	energy: -100, 	researchPoint: 1,	desc: false,	},
    {	id: "karanLaboratory",	type: "research",	techReqs: { karannuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { thorium: -8, caesium: -4, uranium: -25 },	cost: { titanium: 1e8, nanotube: 2e6, engine: 25e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: -1e3,	researchPoint: 8e3,	desc: false,	},
    {	id: "fluidodynamicsCenter",	type: "research",	techReqs: null,	envs: ["gas"],	prod: { hydrogen: -5 },	cost: { titanium: 1e4, plastic: 1e3 },	mult: { titanium: 1.5, plastic: 2 },	buildable: true,	energy: -70, 	researchPoint: 10,	desc: false,	},
    {	id: "karanLaboratory2",	type: "research",	techReqs: { karannuclear: 1 },	envs: ["radioactive"],	prod: { thorium: -8, caesium: -4, uranium: -25 },	cost: { titanium: 100e6, nanotube: 2e6, engine: 25e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: -1e3,	researchPoint: 16e3,	desc: false,	},
    {	id: "superfluidsCenter",	type: "research",	techReqs: { protohalean: 2 },	envs: ["ocean", "ammonia"],	prod: { qaser: -1, superconductor: -10, coolant: -1e3 }, 	cost: { nanotube: 20e6, engine: 3e6, meissnerium: 5e3 },	mult: { meissnerium: 1.3, nanotube: 1.25, engine: 1.3 },	buildable: true,	energy: -38e3,	researchPoint: 25e4,	desc: false,	},
    {	id: "haleanLaboratory",	type: "research",	techReqs: { halean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "radioactive", "ammonia"],	prod: { technetium: -2 },	cost: { circuit: 5e5, nanotube: 2e3, technetium: 100 }, 	mult: { circuit: 1.5, nanotube: 1.3, technetium: 1.2 },	buildable: true,	energy: -500,	researchPoint: 90,	desc: false,	},
    {	id: "vulcanObservatory",	type: "research",	techReqs: { vulcan: 1 },	envs: ["lava", "acid"],	prod: { sulfur: -3 },	cost: { technetium: 1e5, graphite: 2e6 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -800,	researchPoint: 1,	desc: false,	},
    
    {	id: "shipyard",	type: "other",	techReqs: { astronomy: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: null,	cost: { steel: 5e3, titanium: 1e3 },	mult: { steel: 2, titanium: 3.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "tradeHub",	type: "other",	techReqs: { astronomy: 5 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: null,	cost: { steel: 5e4, titanium: 1e4 },	mult: { steel: 2, titanium: 3.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "wahrianTimeMachine",	type: "other",	techReqs: { secret: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { iron: 5e11, steel: 1e12, titanium: 1e10 },	mult: { iron: 1.5, steel: 2, titanium: 2.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateAlpha",	type: "other",	techReqs: { secret: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { iron: 5e11, steel: 1e12, titanium: 1e10 },	mult: { iron: 1.5, steel: 2, titanium: 2.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateBeta",	type: "other",	techReqs: { secret: 2 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { osmium: 5e7, rhodium: 1e8 },	mult: { rhodium: 2, osmium: 2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateGamma",	type: "other",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { osmium: 5e7, rhodium: 1e8 },	mult: { rhodium: 2, osmium: 2 }, notBuildable: !0,	buildable: false,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateDelta",	type: "other",	techReqs: null,	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { osmium: 5e7, rhodium: 1e8 },	mult: { rhodium: 2, osmium: 2 }, notBuildable: !0,	buildable: false,	energy: 0,	researchPoint: 0,	desc: true,	},
]

var getBuildingRef = function(id) { return buildingRefs.find(ref => ref.id == id) }

var shipRefs = [

    {	id: "vitha",	civIds: ["human"],	type: "colony",	shipyardLevel: 1,	militaryValue: 0,	techReqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .8,	piercing: 0,	storage: 800,	fuel: "fuel",	weight: 2,	hp: 1,	cost: { iron: 5e4, steel: 8e4, titanium: 5e3 },	desc: false,	},
    {	id: "zb03",	civIds: ["human"],	type: "cargo",	shipyardLevel: 1,	militaryValue: 0,	techReqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .5,	piercing: 0,	storage: 1e4,	fuel: "fuel",	weight: 2200,	hp: 2,	cost: { steel: 8e4, titanium: 5e3 },	desc: false,	},
    {	id: "zb04",	civIds: ["human"],	type: "cargo",	shipyardLevel: 2,	militaryValue: 0,	techReqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .8,	piercing: 0,	storage: 8e3,	fuel: "fuel",	weight: 2e3,	hp: 2,	cost: { steel: 8e4, titanium: 1e4 },	desc: false,	},
    {	id: "zb22",	civIds: ["human"],	type: "cargo",	shipyardLevel: 7,	militaryValue: 0,	techReqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 5,	speed: 1.2,	piercing: 0,	storage: 2e5,	fuel: "uranium",	weight: 2500,	hp: 10,	cost: { titanium: 1e5, plastic: 2e4, circuit: 500 },	desc: false,	},
    {	id: "zb50",	civIds: ["human"],	type: "cargo",	shipyardLevel: 9,	militaryValue: 0,	techReqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 10,	speed: .8,	piercing: 0,	storage: 5e6,	fuel: "hydrogen",	weight: 3500,	hp: 20,	cost: { plastic: 35e3, circuit: 1500 },	desc: false,	},
    {	id: "orion",	civIds: ["human"],	type: "cargo",	shipyardLevel: 14,	militaryValue: 0,	techReqs: null,	weapon: "laser",	power: 33,	shield: 0,	armor: 20,	speed: 1.5,	piercing: 0,	storage: 250e6,	fuel: "hydrogen",	weight: 5e6,	hp: 150,	cost: { robot: 200, nanotube: 2e4 },	desc: false,	},
    {	id: "ark22",	civIds: ["human"],	type: "fighter",	shipyardLevel: 3,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 10,	shield: 0,	armor: 100,	speed: 2.8,	piercing: 0,	storage: 100,	fuel: "uranium",	weight: 100,	hp: 70,	cost: { steel: 2e5, titanium: 1e4, plastic: 200 },	desc: false,	},
    {	id: "ark55",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 4,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 6,	shield: 0,	armor: 800,	speed: 2.3,	piercing: 0,	storage: 500,	fuel: "uranium",	weight: 150,	hp: 180,	cost: { steel: 2e5, titanium: 1e4, plastic: 200 },	desc: false,	},
    {	id: "foxar",	civIds: ["human"],	type: "fighter",	shipyardLevel: 5,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 120,	shield: 0,	armor: 2e3,	speed: 1.8,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 550,	hp: 850,	cost: { titanium: 1e5, plastic: 5e3, circuit: 500 },	desc: false,	},
    {	id: "skydragon",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 6,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 80,	shield: 0,	armor: 5e3,	speed: 1.3,	piercing: 0,	storage: 1e4,	fuel: "uranium",	weight: 800,	hp: 2200,	cost: { titanium: 8e4, plastic: 8e3, circuit: 500 },	desc: false,	},
    {	id: "babayaga",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 8,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 600,	shield: 0,	armor: 8e3,	speed: 1,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1500,	hp: 5e3,	cost: { plastic: 25e3, circuit: 2e3, ammunition: 550 },	desc: false,	},
    {	id: "luxis",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 10,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 500,	shield: 0,	armor: 1e3,	speed: 5.2,	piercing: 25,	storage: 100,	fuel: "hydrogen",	weight: 220,	hp: 5e3,	cost: { circuit: 12e3, ammunition: 7e3, nanotube: 3e3 },	desc: false,	},
    {	id: "muralla",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 10,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 11,	shield: 8e4,	armor: 5e4,	speed: .15,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 22e3,	hp: 6e4,	cost: { circuit: 5e3, ammunition: 1e4, nanotube: 12e3 },	desc: false,	},
    {	id: "siber",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 11,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 1e4,	shield: 3e4,	armor: 25e3,	speed: .7,	piercing: 8,	storage: 1e6,	fuel: "hydrogen",	weight: 4e3,	hp: 15e3,	cost: { ammunition: 7e4, nanotube: 15e3, robot: 150 },	desc: false,	},
    {	id: "alkantara",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 13,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 21e3,	shield: 5e4,	armor: 3e5,	speed: .15,	piercing: 0,	storage: 2e6,	fuel: "hydrogen",	weight: 55e3,	hp: 6e5,	cost: { ammunition: 25e4, nanotube: 5e4, robot: 1800 },	desc: true,	},
    {	id: "auxilia",	civIds: ["human"],	type: "fighter",	shipyardLevel: 14,	militaryValue: 1,	techReqs: { artofwar: 3 },	weapon: "thermal",	power: 5800,	shield: 0,	armor: 3200,	speed: 1.7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 1340,	hp: 380,	cost: { technetium: 300, tammunition: 10 },	desc: false,	},
    {	id: "servant",	civIds: ["human"],	type: "fighter",	shipyardLevel: 15,	militaryValue: 1,	techReqs: { osmium: 1 },	weapon: "thermal",	power: 3e4,	shield: 0,	armor: 1e4,	speed: 2.8,	piercing: 0,	storage: 10,	fuel: "rhodium",	weight: 130,	hp: 2e4,	cost: { silicon: 5e4, rhodium: 1500, mkembryo: 10 },	desc: false,	},
    {	id: "munya",	civIds: ["human"],	type: "fighter",	shipyardLevel: 19,	militaryValue: 1,	techReqs: { protohalean: 1 },	weapon: "antimatter",	power: 1e6,	shield: 0,	armor: 2e5,	speed: 15.5,	piercing: 25,	storage: 1e3,	fuel: "hydrogen",	weight: 12e5,	hp: 2e6,	cost: { ammunition: 1e5, tammunition: 1e5, nanotube: 88e4, robot: 25e3, engine: 15e3, qaser: 20 },	desc: false,	},
    {	id: "miner",	civIds: ["human"],	type: "mining",	shipyardLevel: 18,	militaryValue: 0,	techReqs: { spacemining: 1 },	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 100e6,	hp: 10,	cost: { iron: 300e6, steel: 1e9, titanium: 100e6, nanotube: 1e6, fullbattery: 25e4, robot: 1e5, engine: 500 },	desc: true,	},
    {	id: "andromeda",	civIds: ["human"],	type: "mining",	shipyardLevel: 17,	militaryValue: 0,	techReqs: { spacemining: 1 },	weapon: "laser",	power: 50,	shield: 0,	armor: 50,	speed: 2,	piercing: 0,	storage: 30e9,	fuel: "hydrogen",	weight: 50e6,	hp: 1e3,	cost: { robot: 2e4, nanotube: 2e6, engine: 1e3 },	desc: false,	},
    {	id: "anger",	civIds: ["human"],	type: "orbital",	shipyardLevel: 16,	militaryValue: 1,	techReqs: { quantum: 3 },	weapon: "antimatter",	power: 300e6,	shield: 1e6,	armor: 50e6,	speed: .035,	piercing: 0,	storage: 100e6,	fuel: "hydrogen",	weight: 5e6,	hp: 5e9,	cost: { iron: 200e9, steel: 2e12, titanium: 50e9, nanotube: 500e6, ammunition: 1e9, fullbattery: 350e6, uammunition: 100e6, armor: 10e6, robot: 1e6, engine: 3e5, antimatter: 1e4 },	desc: true,	},
    {	id: "soul",	civIds: ["human"],	type: "orbital",	shipyardLevel: 20,	militaryValue: 1,	techReqs: { darkmatter: 1 },	weapon: "antimatter",	power: 50e9,	shield: 5e6,	armor: 500e6,	speed: .02,	piercing: 0,	storage: 1e9,	fuel: "hydrogen",	weight: 5e9,	hp: 500e9,	cost: { iron: 5e12, steel: 50e12, titanium: 500e9, nanotube: 30e9, ammunition: 100e9, fullbattery: 1e9, uammunition: 10e9, tammunition: 100e6, armor: 1e9, robot: 1e9, engine: 30e6, antimatter: 10e6, meissnerium: 1e6, darkmatter: 1e3 },	desc: true,	},

    {	id: "glassBurson",	civIds: ["council"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 2800,	shield: 0,	armor: 12e3,	speed: .5,	piercing: 0,	storage: 1.2e6,	fuel: "hydrogen",	weight: 15300,	hp: 1e4,	cost: null,	desc: false,	},    
    {	id: "key",	civIds: ["council"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 4e3,	shield: 0,	armor: 18e3,	speed: .3,	piercing: 0,	storage: 5e6,	fuel: "hydrogen",	weight: 13e3,	hp: 103e3,	cost: null,	desc: false,	},
    {	id: "ark55b",	civIds: ["council", "pirate"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 6,	shield: 0,	armor: 500,	speed: 1.4,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 150,	hp: 50,	cost: null,	desc: false,	},
    {	id: "arkprp",	civIds: ["council", "pirate"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 26,	shield: 0,	armor: 1500,	speed: 1.4,	piercing: 0,	storage: 100,	fuel: "uranium",	weight: 180,	hp: 200,	cost: null,	desc: false,	},
    {	id: "union",	civIds: ["council"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 1e15,	shield: 1e7,	armor: 25e9,	speed: 17.25,	piercing: 0,	storage: 8e6,	fuel: "hydrogen",	weight: 5e9,	hp: 5e14,	cost: null,	desc: false,	},
    {	id: "devil",	civIds: ["council", "andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 124e3,	shield: 0,	armor: 5e4,	speed: 4,	piercing: 0,	storage: 1e3,	fuel: "rhodium",	weight: 500,	hp: 25e3,	cost: null,	desc: false,	},
    {	id: "castilla",	civIds: ["council", "andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 80,	shield: 3e4,	armor: 1e6,	speed: .05,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e4,	hp: 5e6,	cost: null,	desc: false,	},
    {	id: "salantara",	civIds: ["council", "andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 4e12,	shield: 1e4,	armor: 1e9,	speed: 3.12,	piercing: 0,	storage: 10e6,	fuel: "hydrogen",	weight: 1e9,	hp: 2e12,	cost: null,	desc: false,	},
    {	id: "bellerophon",	civIds: ["council", "andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 25e12,	shield: 5e4,	armor: 8e9,	speed: 5.25,	piercing: 0,	storage: 5e6,	fuel: "hydrogen",	weight: 1.5e9,	hp: 4e12,	cost: null,	desc: false,	},

    {	id: "mastodon",	civIds: ["phantid"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 22e5,	shield: 0,	armor: 5e5,	speed: .2,	piercing: 0,	storage: 5e7,	fuel: "uranium",	weight: 16e4,	hp: 5e6,	cost: null,	desc: false,	},
    {	id: "ambjenze",	civIds: ["phantid"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 5e3,	shield: 0,	armor: 3e3,	speed: 1.5,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 1600,	hp: 5e3,	cost: null,	desc: false,	},
    {	id: "zannsarig",	civIds: ["phantid"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 18e3,	shield: 0,	armor: 9e3,	speed: .4,	piercing: 0,	storage: 500,	fuel: "uranium",	weight: 5e3,	hp: 22e3,	cost: null,	desc: false,	},

    {	id: "swarm",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 3e4,	shield: 0,	armor: 1e4,	speed: 5.8,	piercing: 0,	storage: 1e3,	fuel: "rhodium",	weight: 100,	hp: 2e4,	cost: null,	desc: false,	},
    {	id: "enslavedHuman",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 2e6,	shield: 0,	armor: 5e5,	speed: 1.8,	piercing: 0,	storage: 1e4,	fuel: "rhodium",	weight: 1e4,	hp: 2e6,	cost: null,	desc: false,	},
    {	id: "enslavedQuris",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 3e6,	shield: 0,	armor: 2.5e6,	speed: 2.8,	piercing: 0,	storage: 1e4,	fuel: "rhodium",	weight: 1e3,	hp: 5e6,	cost: null,	desc: false,	},
    {	id: "enslavedHalean",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 5e6,	shield: 0,	armor: 1e6,	speed: 3.8,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 2500,	hp: 3e6,	cost: null,	desc: false,	},
    {	id: "heartSwarm",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 2e9,	shield: 0,	armor: 200e6,	speed: .8,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e5,	hp: 1.6e9,	cost: null,	desc: false,	},
    {	id: "aureaPina",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 20e9,	shield: 0,	armor: 10e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e6,	hp: 15e9,	cost: null,	desc: false,	},

    {	id: "haleanSpear",	civIds: ["halean"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 1e4,	shield: 0,	armor: 2e3,	speed: 4.8,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 800,	hp: 2e4,	cost: null,	desc: false,	},
    {	id: "haleanCounselor",	civIds: ["halean"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 9e4,	shield: 0,	armor: 8e3,	speed: 1.2,	piercing: 0,	storage: 2e5,	fuel: "rhodium",	weight: 580,	hp: 12e4,	cost: null,	desc: false,	},
    {	id: "juiniDaughter",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 15e6,	shield: 0,	armor: 2e6,	speed: 1.2,	piercing: 0,	storage: 24e6,	fuel: "rhodium",	weight: 1800,	hp: 872e4,	cost: null,	desc: false,	},
    {	id: "azureHuang",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 221e6,	shield: 0,	armor: 5e6,	speed: .5,	piercing: 0,	storage: 24e6,	fuel: "rhodium",	weight: 5e3,	hp: 872e4,	cost: null,	desc: false,	},
    {	id: "dreamJuini",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 50e6,	shield: 0,	armor: 1e6,	speed: .2,	piercing: 0,	storage: 100e9,	fuel: "rhodium",	weight: 1e6,	hp: 1e9,	cost: null,	desc: false,	},
    {	id: "siren",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 1e6,	shield: 0,	armor: 27e3,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1e3,	hp: 8e6,	cost: null,	desc: false,	},

    {	id: "auxilia",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 5800,	shield: 0,	armor: 3200,	speed: 2.7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 1340,	hp: 380,	cost: null,	desc: false,	},
    {	id: "augustus",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 15e6,	shield: 0,	armor: 1.2e6,	speed: .7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 6e3,	hp: 100e6,	cost: null,	desc: false,	},
    {	id: "leonidas",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 70e6,	shield: 0,	armor: 4e6,	speed: .7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 8e3,	hp: 200e6,	cost: null,	desc: false,	},
    {	id: "alexander",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 180e6,	shield: 0,	armor: 15e6,	speed: .7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 12e3,	hp: 800e6,	cost: null,	desc: false,	},
    {	id: "cerberus",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 200e6,	shield: 0,	armor: 20e6,	speed: .4,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 18e3,	hp: 400e6,	cost: null,	desc: false,	},
    {	id: "charon",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 300e6,	shield: 0,	armor: 10e6,	speed: .4,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 22800,	hp: 400e6,	cost: null,	desc: false,	},
    {	id: "lucifer",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 1.3e9,	shield: 0,	armor: 50e6,	speed: .2,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3e4,	hp: 5e9,	cost: null,	desc: false,	},
    {	id: "deadSoul",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "technetium",	power: 10e6,	shield: 0,	armor: 8e4,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1800,	hp: 35e6,	cost: null,	desc: false,	},

    {	id: "natsumiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 3e6,	shield: 0,	armor: 2e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	hp: 2e6,	cost: null,	desc: false,	},
    {	id: "harumiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 2e6,	shield: 0,	armor: 5e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	hp: 5e6,	cost: null,	desc: false,	},
    {	id: "akimiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 2e6,	shield: 0,	armor: 5e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	hp: 5e6,	cost: null,	desc: false,	},
    {	id: "fuyumiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 1e6,	shield: 0,	armor: 8e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	hp: 8e6,	cost: null,	desc: false,	},
    {	id: "zero",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 1800,	shield: 0,	armor: 200,	speed: 3.5,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 300,	hp: 500,	cost: null,	desc: false,	},
    {	id: "reppu",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 4e3,	shield: 0,	armor: 800,	speed: 1.8,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 700,	hp: 2200,	cost: null,	desc: false,	},

    {	id: "blackStar",	civIds: ["pirate"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 57e6,	shield: 0,	armor: 8e5,	speed: .1,	piercing: 0,	storage: 30e6,	fuel: "hydrogen",	weight: 8e5,	hp: 5E8,	cost: null,	desc: false,	},
    {	id: "noName",	civIds: ["pirate"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 180,	shield: 0,	armor: 2900,	speed: 1.5,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 500,	hp: 200,	cost: null,	desc: false,	},
    {	id: "angelEyes",	civIds: ["pirate"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 260,	shield: 0,	armor: 5e3,	speed: 1.5,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 500,	hp: 200,	cost: null,	desc: false,	},
    {	id: "tucoRamirez",	civIds: ["pirate"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 150,	shield: 0,	armor: 1500,	speed: 1.8,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 350,	hp: 200,	cost: null,	desc: false,	},

    {	id: "marduk",	civIds: ["orion"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 100e6,	shield: 0,	armor: 500e6,	speed: .1,	piercing: 0,	storage: 80,	fuel: "uranium",	weight: 28e6,	hp: 2e12,	cost: null,	desc: false,	},
    {	id: "aurora",	civIds: ["orion"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 15e4,	shield: 0,	armor: 25e3,	speed: .1,	piercing: 0,	storage: 5e7,	fuel: "uranium",	weight: 8200,	hp: 8e5,	cost: null,	desc: false,	},
    {	id: "glissard",	civIds: ["orion"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 18e3,	shield: 0,	armor: 5800,	speed: 1.2,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 7e3,	hp: 6e3,	cost: null,	desc: false,	},
    {	id: "nabonidus",	civIds: ["orion"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 12e3,	shield: 0,	armor: 9200,	speed: .8,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 8e3,	hp: 1e4,	cost: null,	desc: false,	},

    {	id: "dieSchoene",	civIds: ["traum"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 3e5,	shield: 0,	armor: 12e4,	speed: .4,	piercing: 0,	storage: 5e7,	fuel: "hydrogen",	weight: 55e3,	hp: 12e5,	cost: null,	desc: false,	},
    {	id: "alptraum",	civIds: ["traum"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 6600,	shield: 0,	armor: 600,	speed: .9,	piercing: 0,	storage: 1.5e6,	fuel: "hydrogen",	weight: 5600,	hp: 9e3,	cost: null,	desc: false,	},
    {	id: "engel",	civIds: ["traum"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 600,	shield: 0,	armor: 150,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1800,	hp: 3e3,	cost: null,	desc: false,	},

    {	id: "cherub",	civIds: ["wahrian"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 12e4,	shield: 3e3,	armor: 1e4,	speed: 1.8,	piercing: 0,	storage: 1e6,	fuel: "rhodium",	weight: 3980,	hp: 1.8e6,	cost: null,	desc: false,	},
    {	id: "seraph",	civIds: ["wahrian"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 6e6,	shield: 5e3,	armor: 5e4,	speed: .7,	piercing: 0,	storage: 1e6,	fuel: "rhodium",	weight: 11e3,	hp: 20e6,	cost: null,	desc: false,	},
    {	id: "jericho",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 2e12,	shield: 1e4,	armor: 50e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	hp: 200e9,	cost: null,	desc: false,	},
    {	id: "sodom",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 1e12,	shield: 1e4,	armor: 20e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	hp: 130e9,	cost: null,	desc: false,	},
    {	id: "gomorrah",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 1e12,	shield: 1e4,	armor: 20e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	hp: 130e9,	cost: null,	desc: false,	},
    {	id: "zion",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 500e9,	shield: 1e4,	armor: 10e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	hp: 50e9,	cost: null,	desc: false,	},

    {	id: "aster",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 8e12,	shield: 1e4,	armor: 1e9,	speed: 2.2,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 100e6,	hp: 1e12,	cost: null,	desc: false,	},
    {	id: "azalea",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 21e12,	shield: 1e4,	armor: 2e9,	speed: 3.3,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 200e6,	hp: 1200e9,	cost: null,	desc: false,	},
    {	id: "dahlia",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 56e12,	shield: 1e4,	armor: 3e9,	speed: 4.4,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 300e6,	hp: 1500e9,	cost: null,	desc: false,	},
    {	id: "freesia",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 124e12,	shield: 1e4,	armor: 4e9,	speed: 5.5,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 500e6,	hp: 1700e9,	cost: null,	desc: false,	},

    {	id: "wingsPegasus",	civIds: ["andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 1e14,	shield: 1e5,	armor: 25e9,	speed: 7.25,	piercing: 0,	storage: 8e6,	fuel: "hydrogen",	weight: 5e9,	hp: 15e12,	cost: null,	desc: false,	},

    {	id: "nux",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 2e5,	shield: 0,	armor: 1e5,	speed: 2,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 3800,	hp: 1e6,	cost: null,	desc: false,	},
    {	id: "max",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 1e6,	shield: 0,	armor: 15e4,	speed: 1.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 8e3,	hp: 5e5,	cost: null,	desc: false,	},
    {	id: "furiosa",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 1e6,	shield: 0,	armor: 1e4,	speed: 5.5,	piercing: 5,	storage: 0,	fuel: "hydrogen",	weight: 500,	hp: 8e3,	cost: null,	desc: false,	},
    {	id: "angharad",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 1e12,	shield: 0,	armor: 1e9,	speed: 1.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e9,	hp: 1e11,	cost: null,	desc: false,	},
    {	id: "ace",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 28e14,	shield: 0,	armor: 1e12,	speed: 3.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	hp: 62e11,	cost: null,	desc: false,	},

    {	id: "sean",	civIds: ["juinika"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 1e16,	shield: 0,	armor: 1e12,	speed: 15.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	hp: 5e16,	cost: null,	desc: false,	},
    {	id: "dion",	civIds: ["juinika"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 5e16,	shield: 0,	armor: 100e12,	speed: 5.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	hp: 8e16,	cost: null,	desc: false,	},
    {	id: "gradh",	civIds: ["juinika"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "antimatter",	power: 3e16,	shield: 0,	armor: 1e12,	speed: 8.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	hp: 15e16,	cost: null,	desc: false,	},

    {	id: "alecto",	civIds: ["arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 100e12,	shield: 0,	armor: 1e12,	speed: 6,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	hp: 15e14,	cost: null,	desc: false,	},
    {	id: "maegera",	civIds: ["arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 500e12,	shield: 0,	armor: 1e12,	speed: 6,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	hp: 35e14,	cost: null,	desc: false,	},
    {	id: "tisiphon",	civIds: ["arcadia"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "ballistic",	power: 8e15,	shield: 0,	armor: 1e12,	speed: 6,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	hp: 55e15,	cost: null,	desc: false,	},

    {	id: "lightMexager",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 1e15,	shield: 0,	armor: 1e12,	speed: 51,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	hp: 8e15,	cost: null,	desc: false,	},
    {	id: "lightCompanion",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 12e15,	shield: 0,	armor: 1e12,	speed: 34,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	hp: 26e15,	cost: null,	desc: false,	},
    {	id: "vela",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 12e16,	shield: 0,	armor: 1e15,	speed: 12,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 5e12,	hp: 7e17,	cost: null,	desc: false,	},
    {	id: "yola",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 54e17,	shield: 0,	armor: 1e18,	speed: 8,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 50e12,	hp: 11e18,	cost: null,	desc: false,	},
    {	id: "thunder",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "laser",	power: 1e12,	shield: 0,	armor: 1e6,	speed: 32,	piercing: 11,	storage: 0,	fuel: "hydrogen",	weight: 1e9,	hp: 8e12,	cost: null,	desc: false,	},

    {	id: "darklordNest",	civIds: ["darkarmy"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 1e30,	shield: 18e14,	armor: 1e18,	speed: 105,	piercing: 66,	storage: 0,	fuel: "darkmatter",	weight: 1e14,	hp: 1e39,	cost: null,	desc: false,	},
    {	id: "munch",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 1e24,	shield: 500e12,	armor: 1e15,	speed: 99,	piercing: 55,	storage: 0,	fuel: "darkmatter",	weight: 1e12,	hp: 33e24,	cost: null,	desc: false,	},
    {	id: "goya",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 1e21,	shield: 10e12,	armor: 1e12,	speed: 77,	piercing: 44,	storage: 0,	fuel: "darkmatter",	weight: 350e9,	hp: 15e21,	cost: null,	desc: false,	},
    {	id: "goghVan",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 1e18,	shield: 500e6,	armor: 1e9,	speed: 55,	piercing: 33,	storage: 0,	fuel: "darkmatter",	weight: 180e9,	hp: 8e18,	cost: null,	desc: false,	},
    {	id: "matisse",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 1e15,	shield: 80e6,	armor: 1e6,	speed: 33,	piercing: 22,	storage: 0,	fuel: "darkmatter",	weight: 50e9,	hp: 5e15,	cost: null,	desc: false,	},
    {	id: "kingNoir",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 10e12,	shield: 10e6,	armor: 1e6,	speed: 66,	piercing: 77,	storage: 0,	fuel: "darkmatter",	weight: 1e9,	hp: 5e12,	cost: null,	desc: false,	},

    {	id: "argentumSpina",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 5e19,	shield: 25e7,	armor: 1e19,	speed: 14,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 100e12,	hp: 1e20,	cost: null,	desc: false,	},
    {	id: "silverCarapax",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 1e17,	shield: 11e7,	armor: 1e24,	speed: 2,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	hp: 5e18,	cost: null,	desc: false,	},
    {	id: "silverCarrier",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 21e17,	shield: 10e6,	armor: 80e12,	speed: 9,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	hp: 3e17,	cost: null,	desc: false,	},
    {	id: "silverServant",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "thermal",	power: 3e17,	shield: 1e6,	armor: 1e12,	speed: 28,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	hp: 5e16,	cost: null,	desc: false,	},

    {	id: "lifeSpark",	civIds: ["wardens"],	type: "defence",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 8e30,	shield: 1800e12,	armor: 1e18,	speed: 133,	piercing: 99,	storage: 0,	fuel: "darkmatter",	weight: 100e12,	hp: 180e33,	cost: null,	desc: false,	},
    {	id: "salvador",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 12e24,	shield: 500e12,	armor: 1e15,	speed: 119,	piercing: 88,	storage: 0,	fuel: "darkmatter",	weight: 1e12,	hp: 16e24,	cost: null,	desc: false,	},
    {	id: "ernst",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 7e21,	shield: 10e12,	armor: 1e12,	speed: 97,	piercing: 77,	storage: 0,	fuel: "darkmatter",	weight: 350e9,	hp: 9e21,	cost: null,	desc: false,	},
    {	id: "magrit",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 4e18,	shield: 500e6,	armor: 1e9,	speed: 75,	piercing: 66,	storage: 0,	fuel: "darkmatter",	weight: 180e9,	hp: 4e18,	cost: null,	desc: false,	},
    {	id: "miro",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	militaryValue: 1,	techReqs: null,	weapon: "darkmatter",	power: 3e15,	shield: 80e6,	armor: 1e6,	speed: 63,	piercing: 55,	storage: 0,	fuel: "darkmatter",	weight: 50e9,	hp: 2e15,	cost: null,	desc: false,	},
]

var getShipRef = function(id) { return shipRefs.find(ref => ref.id == id) }

var techDescUnlock = function(reqLevel, item, level) {
    
    let ret = ""
    
    ret += "<div class='row gx-2 align-items-baseline'><span class='col text-normal'>" + item + "</span>"
    if (level >= reqLevel) ret += "<small class='col-auto text-success'>at level</small><span class='col-auto text-success'>" + reqLevel + "</span></div>"
    else ret += "<small class='col-auto text-normal'>at level</small><span class='col-auto text-white'>" + reqLevel + "</span></div>"
    
    return ret
}

var techDescImprove = function(item, attrib) {

    let ret = ""
    
    ret += "<div class='row gx-2 align-items-baseline'><span class='col text-normal'>" + item + "</span><small class='col-auto text-white'>" + attrib + "</small></div>"
    
    return ret
}

var techRefs = [

    {	id: "astronomy",	costRP: 3e3,	multRP: 4.3,	costTP: 200,	multTP: 2,	techReqs: null,	planetReq: null,	max: -1,	desc: true,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Shipyard", level)
            ret += techDescUnlock(5, "Trade Hub", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Vitha", "Speed")
            
            return ret;
        },
        onLevelUp: function(level) {
      
            if (level >= 1) getShipRef('vitha').speed = (getShipRef('vitha').speed * 1.12).toPrecision(3)
        },
    },
    {	id: "science",	costRP: 4e4,	multRP: 2.5,	costTP: 100,	multTP: 1.5,	techReqs: { astronomy: 1 },	planetReq: null,	max: 64,	desc: true,
        description: function(level) {
        
            var ret = ""
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('laboratory').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('ammoniaAirship').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('oceanographicCenter').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('cryogenicLaboratory').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('karanLaboratory').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('fluidodynamicsCenter').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('karanLaboratory2').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('superfluidsCenter').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('haleanLaboratory').researchPoint *= 1.1 + .001 * level
            if (level >= 1) getBuildingRef('vulcanObservatory').researchPoint *= 1.1 + .001 * level
        },
    },
    {	id: "military",	costRP: 33e3,	multRP: 2.2,	costTP: 50,	multTP: 1.3,	techReqs: { astronomy: 4 },	planetReq: "antirion",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"

            ret += techDescUnlock(1, "Ammunition Factory", level)
            ret += techDescUnlock(8, "Uranium Shell Assembler", level)
            ret += techDescUnlock(12, "Armor Factory", level)
            ret += techDescUnlock(16, "Engine Factory", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Ammunition Factory", "Prod")
            ret += techDescImprove("Uranium Shell Assembler", "Prod")
            ret += techDescImprove("Armor Factory", "Prod")
            ret += techDescImprove("Engine Factory", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 2) getBuildingRef('ammunitionFactory').prod.ammunition *= 1.12
            if (level >= 9) getBuildingRef('uraniumShellAssembler').prod.uammunition *= 1.12
            if (level >= 13) getBuildingRef('armorFactory').prod.armor *= 1.12
            if (level >= 17) getBuildingRef('engineFactory').prod.engine *= 1.12
        },
    },
    {	id: "artofwar",	costRP: 5e8,	multRP: 1.6,	costTP: 50,	multTP: 1.3,	techReqs: { military: 12 },	planetReq: "antaris",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "T-Ammunition Assembler", level)
            ret += techDescUnlock(3, "Auxilia", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Friendly Ships", "Power, Armor, shield, HP")
            ret += techDescImprove("Foxar", "Power, Armor, HP")
            ret += techDescImprove("Sky Dragon", "Power, Armor, HP")
            ret += techDescImprove("Babayaga", "Power, Armor, Shield, HP")
            ret += techDescImprove("Siber", "Power, Armor, Shield, HP")
            ret += techDescImprove("Alkantara", "Power, Armor, Shield, HP")
            
            return ret;
        },
        onLevelUp: function(level) {

            let ships = game.getShipsByCivId("human")
            for (let sId in ships) {
                let ship = ships[sId]
                
                if (sId == 'foxar' || sId == 'skydragon') {
                
                    ship.hp *= 1.12
                    ship.power *= 1.12
                    ship.armor *= 1.12
                }
                else if (sId == 'babayaga' || sId == 'siber' || sId == 'alkantara') {
                
                    let coeff = Math.min(1.05 + .0015 * level, 1.15)
                    
                    ship.hp *= coeff
                    ship.power *= coeff
                    ship.armor *= coeff
                    ship.shield *= coeff
                }
                else {
                
                    ship.hp *= 1.05
                    ship.power *= 1.05
                    ship.armor *= 1.05
                    ship.shield *= 1.05
                }
            }
        },
    },
    {	id: "karanartofwar",	costRP: 2e11,	multRP: 2.2,	costTP: 1200,	multTP: 2,	techReqs: { artofwar: 12 },	planetReq: "alfari",	max: 111,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Improves</div>"
            
            ret += techDescImprove("Friendly Ships", "Piercing")
            ret += techDescImprove("Friendly Ships", "Costs")
            ret += techDescImprove("Luxis", "Weight")
            
            return ret;
        },
        onLevelUp: function(level) {

            let ships = game.getShipsByCivId("human")
            for (let sId in ships) {
                let ship = ships[sId]
                
                ship.piercing *= 1.3
                for (let rId in ship.cost)
                    ships.cost[rId] *= .92
            }

            ships['luxis'].weight *= 1.5
            ships['luxis'].combatWeight *= 1.5
        },
    },
    {	id: "xiranartofwar",	costRP: 15e13,	multRP: 2.2,	costTP: 1e7,	multTP: 2,	techReqs: { karanartofwar: 13 },	planetReq: "xirandrus",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Improves</div>"
            
            ret += techDescImprove("Battery Factory", "Prod")
            ret += techDescImprove("Battery Charger", "Costs")
            ret += techDescImprove("Battery Power Plant", "Costs")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('batteryFactory').prod.emptybattery *= 1.12
            if (level >= 1) getBuildingRef('batteryCharger').cost.steel *= 0.75
            if (level >= 1) getBuildingRef('batteryCharger').cost.titanium *= 0.75
            if (level >= 1) getBuildingRef('batteryCharger').cost.plastic *= 0.75
            if (level >= 1) getBuildingRef('batteryPowerPlant').cost.circuit *= 0.75
            if (level >= 1) getBuildingRef('batteryPowerPlant').cost.titanium *= 0.75
            if (level >= 1) getBuildingRef('batteryPowerPlant').cost.plastic *= 0.75        
        },
    },

    {	id: "mineralogy",	costRP: 125,	multRP: 2.2,	costTP: 10,	multTP: 1.2,	techReqs: null,	planetReq: null,	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(3, "Metal Collector", level)
            ret += techDescUnlock(4, "Sand Quarry", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Graphite Extractor", "Prod")
            ret += techDescImprove("Submerged Metal Mine", "Prod")
            ret += techDescImprove("Submerged Sand Mine", "Prod")
            ret += techDescImprove("Lava Mine", "Prod")
            ret += techDescImprove("Sulfur Mine", "Prod")
            ret += techDescImprove("Rhodium Extractor", "Prod")
            ret += techDescImprove("Metal Collector", "Prod")
            ret += techDescImprove("Sand Quarry", "Prod")
            ret += techDescImprove("Sand Mine", "Prod")
            ret += techDescImprove("Pumpjack", "Prod")
            ret += techDescImprove("Algae Oil Farm", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1 && level < 26)  {

                let value = 1 + (25 - (.2 * level)) / 100
                getBuildingRef('mine').prod.iron += value
            }

            if (level >= 1) getBuildingRef('graphiteExtractor').prod.graphite *= 1.12
            if (level >= 1) getBuildingRef('submergedMetalMine').prod.iron *= 1.12
            if (level >= 1) getBuildingRef('submergedMetalMine').prod.titanium *= 1.12
            if (level >= 1) getBuildingRef('submergedMetalMine').prod.uranium *= 1.12
            if (level >= 1) getBuildingRef('submergedSandMine').prod.sand *= 1.12
            if (level >= 1) getBuildingRef('submergedSandMine').prod.graphite *= 1.12
            if (level >= 1) getBuildingRef('lavaMine').prod.graphite *= 1.12
            if (level >= 1) getBuildingRef('sulfurMine').prod.titanium *= 1.18            
            if (level >= 1) getBuildingRef('rhodiumExtractor').prod.titanium *= 1.18
            if (level >= 4) getBuildingRef('metalCollector').prod.titanium *= 1.18
            if (level >= 4) getBuildingRef('metalCollector').prod.uranium *= 1.18
            if (level >= 4) getBuildingRef('sandQuarry').prod.sand *= 1.12
            if (level >= 4) getBuildingRef('sandMine').prod.sand *= 1.12
            if (level >= 4) getBuildingRef('pumpjack').prod.oil *= 1.08
            if (level >= 4) getBuildingRef('algaeOilFarm').prod.oil *= 1.08
        },
    },
    {	id: "material",	costRP: 500,	multRP: 2.2,	costTP: 15,	multTP: 1.4,	techReqs: { mineralogy: 1 },	planetReq: null,	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(8, "Plastic Factory", level)
            ret += techDescUnlock(8, "Polymerizer", level)
            ret += techDescUnlock(15, "Nanotube Factory", level)
            ret += techDescUnlock(15, "Nanotube Dome", level)
            ret += techDescUnlock(35, "Ceramic Foundry", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Foundry", "Prod")
            ret += techDescImprove("Plastic Factory", "Prod")
            ret += techDescImprove("Polymerizer", "Prod")
            ret += techDescImprove("Nanotube Factory", "Prod")
            ret += techDescImprove("Nanotube Dome", "Prod")
            ret += techDescImprove("Ceramic Foundry", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1 && level <= 100)  {

                let value = 1 + (50 - (.5 * level)) / 100
                getBuildingRef('foundry').prod.steel += value
            }

            if (level >= 8) getBuildingRef('plasticFactory').prod.plastic *= 1.25           
            if (level >= 8) getBuildingRef('polymerizer').prod.plastic *= 1.25
            if (level >= 15) getBuildingRef('nanotubeFactory').prod.nanotube *= 1.12
            if (level >= 15) getBuildingRef('nanotubeDome').prod.nanotube *= 1.12
            if (level >= 36) getBuildingRef('ceramicFoundry').prod.meissnerium *= 1.08
        },
    },
    {	id: "chemical",	costRP: 1200,	multRP: 2.2,	costTP: 12,	multTP: 1.2,	techReqs: { material: 1 },	planetReq: null,	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Pumpjack", level)
            ret += techDescUnlock(1, "Thermal Plant", level)
            ret += techDescUnlock(2, "Oil Refinery", level)
            ret += techDescUnlock(30, "Methane Aggregator", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Methane Processer", "Prod")
            ret += techDescImprove("Methane Harvester", "Prod")
            ret += techDescImprove("Oil Refinery", "Prod")
            ret += techDescImprove("Methane Aggregator", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('methaneProcesser').prod.fuel *= 1.25
            if (level >= 1) getBuildingRef('methaneHarvester').prod.methane *= 1.20
            if (level >= 3) getBuildingRef('oilRefinery').prod.fuel *= 1.12
            if (level >= 31) getBuildingRef('methaneAggregator').prod.fuel *= 1.20
        },
    },
    {	id: "electronic",	costRP: 5e4,	multRP: 2.2,	costTP: 100,	multTP: 1.5,	techReqs: { material: 5 },	planetReq: null,	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Sand Smelter", level)
            ret += techDescUnlock(1, "Electronic Facility", level)
            ret += techDescUnlock(1, "Solar Central", level)
            ret += techDescUnlock(8, "Battery Factory", level)
            ret += techDescUnlock(8, "Battery Power Plant", level)
            ret += techDescUnlock(8, "Battery Charger", level)
            ret += techDescUnlock(30, "Superconductors Factory", level)
            ret += techDescUnlock(35, "Cells Factory", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Electronic Facility", "Prod")
            ret += techDescImprove("Battery Charger", "Prod")
            ret += techDescImprove("Battery Power Plant", "Energy")
            ret += techDescImprove("Superconductors Factory", "Prod")
            ret += techDescImprove("Cells Factory", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 2) getBuildingRef('electronicFacility').prod.circuit *= 1.30
            if (level >= 8) getBuildingRef('batteryCharger').cost.steel *= .5
            if (level >= 8) getBuildingRef('batteryCharger').cost.titanium *= .5
            if (level >= 8) getBuildingRef('batteryCharger').cost.plastic *= .5
            if (level >= 8) getBuildingRef('batteryPowerPlant').cost.circuit *= .5
            if (level >= 8) getBuildingRef('batteryPowerPlant').cost.titanium *= .5
            if (level >= 8) getBuildingRef('batteryPowerPlant').cost.plastic *= .5
            if (level >= 31) getBuildingRef('superconductorsFactory').prod.superconductor *= 1.08

            if (level >= 35 && level <= 60)  {

                let value = 1 + (35 - (.5 * level)) / 100
                getBuildingRef('cellsFactory').prod.meissnercell += value
            }
        },
    },
    {	id: "ice",	costRP: 1e4,	multRP: 2.2,	costTP: 100,	multTP: 1.5,	techReqs: { material: 3 },	planetReq: "vasilis",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Ice Drill", level)
            ret += techDescUnlock(1, "Water Freezer", level)
            ret += techDescUnlock(1, "Ice Melter", level)
            ret += techDescUnlock(10, "Coolant Factory", level)
            ret += techDescUnlock(12, "Cryogenic Laboratoryy", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Ice Drill", "Prod")
            ret += techDescImprove("Coolant Factory", "Prod")
            ret += techDescImprove("Ammonia Refrigerator", "Prod")
            ret += techDescImprove("Cryogenic Laboratory", "RP")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 2) getBuildingRef('iceDrill').prod.ice *= 1.25
            if (level >= 2) getBuildingRef('coolantFactory').prod.coolant *= 1.12            
            if (level >= 10) getBuildingRef('ammoniaRefrigerator').prod.coolant *= 1.12
            if (level >= 12) getBuildingRef('cryogenicLaboratory').researchPoint *= 1.12
        },
    },
    {	id: "nuclear",	costRP: 5e5,	multRP: 2.2,	costTP: 100,	multTP: 1.5,	techReqs: { electronic: 1 },	planetReq: null,	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Hydrogen Condenser", level)
            ret += techDescUnlock(1, "Nuclear Powerplant", level)
            ret += techDescUnlock(3, "Pressurized Water Reactor", level)
            ret += techDescUnlock(5, "Fusion Reactor", level)
            ret += techDescUnlock(5, "Floating Reactor", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Fusion Reactor", "Energy")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 5) getBuildingRef('fusionReactor').prod.hydrogen *= .95
        },
    },
    {	id: "halean",	costRP: 1e8,	multRP: 2.2,	costTP: 100,	multTP: 1.5,	techReqs: { intelligence: 1 },	planetReq: "posirion",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Technetium Fissor", level)
            ret += techDescUnlock(1, "Halean A.I. Center", level)
            ret += techDescUnlock(1, "Halean Laboratory", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Fusion Reactor", "Energy")
            ret += techDescImprove("Robot Factory", "Prod")
            ret += techDescImprove("Halean Laboratory", "RP")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('fusionReactor').prod.technetium *= 1.12
            if (level >= 1) getBuildingRef('haleanLaboratory').researchPoint *= 1.12
            if (level >= 1) getBuildingRef('robotFactory').prod.robot *= 1.12
        },
    },
    {	id: "intelligence",	costRP: 5e7,	multRP: 2.2,	costTP: 10,	multTP: 1.2,	techReqs: { electronic: 8 },	planetReq: "zelera",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Robot Factory", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Robot Factory", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('robotFactory').researchPoint *= 1.12
        },
    },
    {	id: "vulcan",	costRP: 2e8,	multRP: 2.2,	costTP: 100,	multTP: 1.5,	techReqs: { mineralogy: 17 },	planetReq: "nassaus",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Sulfur Mine", level)
            ret += techDescUnlock(1, "Lava Mine", level)
            ret += techDescUnlock(1, "Vulcan Observatory", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Sulfur Mine", "Prod")
            ret += techDescImprove("Lava Mine", "Prod")
            ret += techDescImprove("Vulcan Observatory", "RP")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('sulfurMine').prod.graphite *= 1.12
            if (level >= 1) getBuildingRef('sulfurMine').prod.sulfur *= 1.12
            if (level >= 1) getBuildingRef('lavaMine').prod.titanium *= 1.12
            if (level >= 1) getBuildingRef('vulcanObservatory').researchPoint *= 1.12
        },
    },
    {	id: "hydro",	costRP: 2e4,	multRP: 2.2,	costTP: 50,	multTP: 1.5,	techReqs: { chemical: 3 },	planetReq: "aequoreas",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Water Pump", level)
            ret += techDescUnlock(1, "Submerged Metal Mine", level)
            ret += techDescUnlock(1, "Submerged Sand Mine", level)
            ret += techDescUnlock(1, "Electrolyzer", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Water Pump", "Prod")
            ret += techDescImprove("Pumping Platform", "Prod")
            ret += techDescImprove("Electrolyzer", "Prod")
            ret += techDescImprove("Algae Oil Farm", "Prod")
            ret += techDescImprove("Submerged Oil Refinery", "Prod")
            ret += techDescImprove("Nanotube Dome", "Prod")
            ret += techDescImprove("Oceanographic Center", "RP")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('waterPump').prod.water *= 1.12
            if (level >= 1) getBuildingRef('pumpingPlatform').prod.water *= 1.12
            if (level >= 1) getBuildingRef('electrolyzer').prod.water *= 1.12
            if (level >= 1) getBuildingRef('algaeOilFarm').prod.oil *= 1.12
            if (level >= 1) getBuildingRef('submergedOilRefinery').prod.oil *= 1.08
            if (level >= 1) getBuildingRef('electrolyzer').prod.hydrogen *= 1.12
            if (level >= 1) getBuildingRef('submergedOilRefinery').prod.fuel *= 1.12
            if (level >= 1) getBuildingRef('nanotubeDome').prod.nanotube *= 1.08
            if (level >= 1) getBuildingRef('oceanographicCenter').researchPoint *= 1.12
        },
    },
    {	id: "rhodium",	costRP: 1e9,	multRP: 2.2,	costTP: 500,	multTP: 1.35,	techReqs: { chemical: 17 },	planetReq: "tsartasis",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Rhodium Extractor", level)
            ret += techDescUnlock(1, "Rhodium Sand Smelter", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Rhodium Extractor", "Prod")
            ret += techDescImprove("Rhodium Sand Smelter", "Prod")
            ret += techDescImprove("Sand Smelter", "Prod")
            ret += techDescImprove("Submerged Sand Mine", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 2) getBuildingRef('rhodiumExtractor').prod.rhodium *= 1.12
            if (level >= 2) getBuildingRef('rhodiumExtractor').prod.titanium *= 1.12
            if (level >= 2) getBuildingRef('rhodiumSandSmelter').prod.silicon *= 1.08
            if (level >= 2) getBuildingRef('sandSmelter').prod.silicon *= 1.08
            if (level >= 2) getBuildingRef('submergedSandMine').prod.silicon *= 1.08
        },
    },
    {	id: "osmium",	costRP: 5e9,	multRP: 2.2,	costTP: 10,	multTP: 1.2,	techReqs: { rhodium: 2 },	planetReq: "city",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Osmium Extractor", level)
            ret += techDescUnlock(1, "Metallokopta Clonator", level)
            ret += techDescUnlock(1, "Servant", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Osmium Extractor", "Prod")
            ret += techDescImprove("Metallokopta Clonator", "Prod")
            ret += techDescImprove("Submerged Sand Mine", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('osmiumExtractor').prod.osmium *= 1.05
            if (level >= 1) getBuildingRef('metallokoptaClonator').prod.mkembryo *= 1.05
            if (level >= 2) getBuildingRef('submergedSandMine').prod.osmium *= 1.08
        },
    },
    {	id: "quantum",	costRP: 2e9,	multRP: 2.2,	costTP: 10,	multTP: 1.2,	techReqs: { halean: 4 },	planetReq: "zhura",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Particle Accelerator", level)
            ret += techDescUnlock(1, "Antimatter Collider", level)
            ret += techDescUnlock(3, "Anger of Perseus", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Particle Accelerator", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('particleAccelerator').prod.antimatter *= 1.12
        },
    },
    {	id: "secret",	costRP: 5e10,	multRP: 100,	costTP: 2e3,	multTP: 100,	techReqs:{ quantum: 2,	nuclear: 14,	rhodium: 3 },	planetReq: "zhura",	max: 2,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Wahrian Time Machine", level)
            ret += techDescUnlock(1, "Space Gate Alpha", level)
            ret += techDescUnlock(2, "Space Gate Beta", level)
            
            return ret;
        },
        onLevelUp: function(level) {

        },
    },
    {	id: "spacemining",	costRP: 2e12,	multRP: 3,	costTP: 5e3,	multTP: 2,	techReqs:{ secret: 1 },	planetReq: "swamp",	max: 100,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Medusa Miner", level)
            ret += techDescUnlock(1, "Andromeda Cargo", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Andromeda Cargo", "Storage")
            
            return ret;
        },
        onLevelUp: function(level) {

            let ships = game.getShipsByCivId("human")

            if (level >= 1) ships['andromeda'].storage *= 1.08
        },
    },
    {	id: "karannuclear",	costRP: 25e10,	multRP: 2.2,	costTP: 2e3,	multTP: 1.5,	techReqs:{ quantum: 6 },	planetReq: "xeno",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Thorium Extractor", level)
            ret += techDescUnlock(1, "Thorium Reactor", level)
            ret += techDescUnlock(1, "Karan Laboratory", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("ThoriumReactor", "Prod")
            ret += techDescImprove("ThoriumReactor2", "Prod")
            ret += techDescImprove("ThoriumExtractor", "Prod")
            ret += techDescImprove("PressurizedAmmoniaReactor", "Prod")
            ret += techDescImprove("Pressurized WaterReactor", "Prod")
            ret += techDescImprove("Karan Laboratory", "RP")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('thoriumReactor').energy *= 1.05
            if (level >= 1) getBuildingRef('thoriumReactor2').energy *= 1.05
            if (level >= 2) getBuildingRef('thoriumExtractor').prod.thorium *= 1.25
            if (level >= 2) getBuildingRef('thoriumExtractor').prod.caesium *= 1.25
            if (level >= 1) getBuildingRef('pressurizedAmmoniaReactor').energy *= 1.05
            if (level >= 1) getBuildingRef('pressurizedWaterReactor').energy *= 1.05
            if (level >= 2) getBuildingRef('karanLaboratory').researchPoint *= 1.20
        },
    },
    {	id: "ammonia",	costRP: 6e11,	multRP: 2.2,	costTP: 1e3,	multTP: 1.5,	techReqs:{ hydro: 20 },	planetReq: "auriga",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Ammonia Extractor", level)
            ret += techDescUnlock(1, "Ammonia Electrolyzer", level)
            ret += techDescUnlock(1, "Ammonia Refrigerator", level)
            ret += techDescUnlock(1, "Pressurized Ammonia Reactor", level)
            ret += techDescUnlock(1, "Ammonia Airship", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Ammonia Extractor", "Prod")
            ret += techDescImprove("Ammonia Refrigerator", "Prod")
            ret += techDescImprove("Ammonia Electrolyzer", "Prod")
            ret += techDescImprove("Ammonia Airship", "RP")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('ammoniaAirship').researchPoint *= 1.12
            if (level >= 2) getBuildingRef('ammoniaExtractor').prod.ammonia *= 1.25
            if (level >= 2) getBuildingRef('ammoniaRefrigerator').prod.coolant *= 1.25
            if (level >= 2) getBuildingRef('ammoniaElectrolyzer').prod.ammonia *= 1.25
            if (level >= 2) getBuildingRef('ammoniaElectrolyzer').prod.hydrogen *= 1.25
        },
    },
    {	id: "protohalean",	costRP: 5e12,	multRP: 2.2,	costTP: 1e6,	multTP: 2,	techReqs:{ karannuclear: 20 },	planetReq: "asun",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Qaser Assembler", level)
            ret += techDescUnlock(1, "Munya", level)
            ret += techDescUnlock(2, "Superfluids Center", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Qaser Assembler", "Prod")
            ret += techDescImprove("Superfluids Center", "RP")
            ret += techDescImprove("Friendly Ships", "Power")
            ret += techDescImprove("Munya", "HP, Power")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('qaserAssembler').prod.qaser *= 1.12
            if (level >= 3) getBuildingRef('superfluidsCenter').researchPoint *= 1.25

            let ships = game.getShipsByCivId("human")
            for (let sId in ships) {
                let ship = ships[sId]
                
                if (ship.weapon == "laser")
                    ships.power *= .2
            }

            ships["munya"].hp *= 1.2
            ships["munya"].power *= 1.2
        },
    },
    {	id: "darkmatter",	costRP: 15e13,	multRP: 2.2,	costTP: 1e7,	multTP: 2,	techReqs:{ protohalean: 1 },	planetReq: "death",	max: -1,	desc: false,
        description: function(level) {
        
            var ret = ""
            ret += "<div class='h6 text-gray mb-2'>Unlocks</div>"
            
            ret += techDescUnlock(1, "Darkmatter Refiner", level)
            ret += techDescUnlock(1, "Soul of Andromeda", level)
            
            ret += "<div class='h6 text-gray mb-2 mt-3'>Improves</div>"
            
            ret += techDescImprove("Darkmatter Refiner", "Prod")
            
            return ret;
        },
        onLevelUp: function(level) {

            if (level >= 1) getBuildingRef('darkmatterRefiner').prod.darkmatter *= 1.25
        },
    },
]

var nebulaRefs = [

    {	id: "perseus",	},
    {	id: "andromeda",	},
    {	id: "void",	},
]

var planetRefs = [
    
    {	id: "promision",	nebulaId: "perseus",	civId: "human",	x: 26,	y: 1,	env: "terrestrial",	influence: 1,	radius: 6833,	temp: 22,	atmos: "oxygen",	orbit: 1,	prod:{ graphite: 1, iron: 1, methane: 1, oil : 1, silicon: 1, titanium: 1, uranium: 1, sand: .5 },	},
    {	id: "vasilis",	nebulaId: "perseus",	civId: null,	x: 100,	y: 39,	env: "ice",	influence: 1,	radius: 5201,	temp: -21,	atmos: "oxygen",	orbit: 2.9,	prod:{ coolant: 2, ice: 2, methane: 2, titanium: 1.5, iron: 1, uranium: 1, oil: .5 },	},
    {	id: "aequoreas",	nebulaId: "perseus",	civId: null,	x: 0,	y: 71,	env: "ocean",	influence: 1,	radius: 8890,	temp: 18,	atmos: "oxygen",	orbit: .7,	prod:{ sand: 2, iron: 1, titanium: 1.5, oil: 1, water: 5 },	},
    {	id: "orpheus",	nebulaId: "perseus",	civId: null,	x: 18,	y: 133,	env: "gas",	influence: 1,	radius: 18540,	temp: -141,	atmos: "hydrogen",	orbit: 8.2,	prod:{ hydrogen: 8, methane: 3, technetium: 2 },	},
    {	id: "antirion",	nebulaId: "perseus",	civId: "pirates",	x: 0,	y: 241,	env: "metallic",	influence: 5,	radius: 2230,	temp: -66,	atmos: "methane",	orbit: 5.45,	prod:{ thorium: .2, rhodium: 1.5, iron: 3, titanium: 3, uranium: 2, graphite: .5, methane: 2.4, plastic: 2 },	},
    {	id: "city",	nebulaId: "perseus",	civId: "city",	x: 94,	y: 189,	env: "ice",	influence: 8,	radius: 4235,	temp: -8,	atmos: "oxygen",	orbit: 1.82,	prod:{ thorium: .5, ammunition: 2, uammunition: 1.5, iron: 3, graphite: 3, titanium: 2, uranium: 1.5, ice: 1.2, methane: 2.2, osmium: .5 },	},
    {	id: "ishtar",	nebulaId: "perseus",	civId: "orion",	x: 174,	y: 81,	env: "ocean",	influence: 12,	radius: 7020,	temp: 3,	atmos: "co2",	orbit: 2.8,	prod:{ graphite: 2, sand: 1, uranium: 1, water: 3.2, oil: .5, nanotube: 2 },	},
    {	id: "traumland",	nebulaId: "perseus",	civId: "phantids",	x: 321,	y: 107,	env: "terrestrial",	influence: 15,	radius: 6550,	temp: 28,	atmos: "oxygen",	orbit: .7,	prod:{ iron: 1, titanium: 1.2, sand: 1, oil: 2, uranium: .25, water: .6, silicon: 2, graphite: 2, methane: 1 },	},
    {	id: "tataridu",	nebulaId: "perseus",	civId: "phantids",	x: 273,	y: 1,	env: "terrestrial",	influence: 18,	radius: 3118,	temp: 38,	atmos: "oxygen",	orbit: .45,	prod:{ iron: .5, uranium: 2, oil: 2.6, water: .25, methane: 2.8, circuit: 2 },	},
    {	id: "zelera",	nebulaId: "perseus",	civId: "robot",	x: 554,	y: 85,	env: "ice",	influence: 22,	radius: 8230,	temp: -30,	atmos: "methane",	orbit: 7.45,	prod:{ osmium: 1.8, ice: 3, iron: 4, titanium: 3.5, uranium: 2.2, robot: 2, methane: 2 },	},
    {	id: "posirion",	nebulaId: "perseus",	civId: "halean",	x: 634,	y: 0,	env: "gas",	influence: 27,	radius: 48270,	temp: -189,	atmos: "hydrogen",	orbit: 17.22,	prod:{ hydrogen: 51.3, methane: 18.41, technetium: 2 },	},
    {	id: "tsartasis",	nebulaId: "perseus",	civId: "metallokopta",	x: 500,	y: 426,	env: "desert",	influence: 29,	radius: 4504,	temp: 102,	atmos: "co2",	orbit: .31,	prod:{ iron: 2.7, titanium: 3.7, uranium: 5.4, sand: 2, rhodium: 1.6 },	},
    {	id: "acanthus",	nebulaId: "perseus",	civId: "quris",	x: 188,	y: 223,	env: "ice",	influence: 31,	radius: 5155,	temp: -12,	atmos: "co2",	orbit: 2.4,	prod:{ darkmatter: 2, thorium: 1.5, osmium: 2.2, methane: 3.2, ice: 5, titanium: 2, uranium: 3 },	},
    {	id: "raknar",	nebulaId: "perseus",	civId: "quris",	x: 124,	y: 254,	env: "ice",	influence: 32,	radius: 4081,	temp: -31,	atmos: "co2",	orbit: 4.5,	prod:{ thorium: .5, osmium: 1.8, methane: 2, ice: 3, titanium: 3, uranium: 1, iron: 2, graphite: 5 },	},
    {	id: "shin",	nebulaId: "perseus",	civId: "metallokopta",	x: 469,	y: 518,	env: "ice",	influence: 32,	radius: 4504,	temp: -85,	atmos: "co2",	orbit: 8.31,	prod:{ iron: 4.1, titanium: 3.5, uranium: 2.2, ice: 3.2, water: 2, osmium: 3.7, mkembryo: 2 },	},
    {	id: "phorun",	nebulaId: "perseus",	civId: "halean",	x: 783,	y: 62,	env: "gas",	influence: 33,	radius: 28270,	temp: -89,	atmos: "hydrogen",	orbit: 7.22,	prod:{ hydrogen: 31.3, methane: 28.41, technetium: 2.8 },	},
    {	id: "antaris",	nebulaId: "perseus",	civId: "quris",	x: 263,	y: 386,	env: "metallic",	influence: 34,	radius: 6052,	temp: 32,	atmos: "co2",	orbit: 1.4,	prod:{ thorium: 1.2, tammunition: 2, methane: 2, titanium: 1, uranium: 2, iron: 3, rhodium: .5 },	},
    {	id: "teleras",	nebulaId: "perseus",	civId: "quris",	x: 405,	y: 297,	env: "metallic",	influence: 36,	radius: 10733,	temp: 202,	atmos: "methane",	orbit: .2,	prod:{ methane: 3, titanium: 2, uranium: 3, iron: 1, rhodium: 1 },	},
    {	id: "jabir",	nebulaId: "perseus",	civId: "quris",	x: 321,	y: 318,	env: "metallic",	influence: 38,	radius: 8220,	temp: 182,	atmos: "methane",	orbit: .26,	prod:{ caesium: 1, methane: 2, titanium: 4, uranium: 1, iron: 2, rhodium: 1.2 },	},
    {	id: "epsilon",	nebulaId: "perseus",	civId: "halean",	x: 706,	y: 154,	env: "desert",	influence: 46,	radius: 12301,	temp: 74,	atmos: "co2",	orbit: .44,	prod:{ sand: 8, iron: 4.82, titanium: 1.35, uranium: .2, rhodium: 2.5, silicon: 2 },	},
    {	id: "zhura",	nebulaId: "perseus",	civId: "halean",	x: 830,	y: 242,	env: "gas",	influence: 57,	radius: 36420,	temp: -135,	atmos: "hydrogen",	orbit: 5.8,	prod:{ darkmatter: 2, hydrogen: 80, methane: 51.5, technetium: 3.5 },	},
    {	id: "kitrion",	nebulaId: "perseus",	civId: "metallokopta",	x: 515,	y: 228,	env: "desert",	influence: 57,	radius: 4504,	temp: 77,	atmos: "co2",	orbit: .77,	prod:{ darkmatter: 2, caesium: 3, methane: 3.2, hydrogen: 15.4, antimatter: 2 },	},
    {	id: "ares",	nebulaId: "perseus",	civId: "metallokopta",	x: 109,	y: 461,	env: "desert",	influence: 63,	radius: 3402,	temp: 84,	atmos: "co2",	orbit: 1.42,	prod:{ oil: 2.09, iron: 2.81, titanium: 2.2, sand: 8.33, uranium: 1.37, rhodium: 1.8 },	},
    {	id: "kandi",	nebulaId: "perseus",	civId: "metallokopta",	x: 254,	y: 531,	env: "ice",	influence: 65,	radius: 4504,	temp: -22,	atmos: "co2",	orbit: 3.31,	prod:{ iron: 1.76, titanium: 1.75, uranium: 1.2, ice: 4.8, osmium: 1.9, rhodium: 2.5 },	},
    {	id: "bharash",	nebulaId: "perseus",	civId: "halean",	x: 898,	y: 355,	env: "gas",	influence: 68,	radius: 45420,	temp: -92,	atmos: "hydrogen",	orbit: 3.01,	prod:{ hydrogen: 89, methane: 48.5, technetium: 5 },	},
    {	id: "caerul",	nebulaId: "perseus",	civId: "halean",	x: 835,	y: 120,	env: "gas",	influence: 75,	radius: 21155,	temp: -122,	atmos: "hydrogen",	orbit: 9.84,	prod:{ antimatter: 2, hydrogen: 8.66, methane: 3.22, technetium: 8 },	},
    {	id: "mermorra",	nebulaId: "perseus",	civId: "metallokopta",	x: 607,	y: 327,	env: "gas",	influence: 82,	radius: 88706,	temp: 305,	atmos: "hydrogen",	orbit: .31,	prod:{ iron: 3.2, titanium: 2.66, uranium: 1.95, silicon: 7.5, sand: 2.4, rhodium: 2.54, oil: 2.7 },	},
    {	id: "xora2",	nebulaId: "perseus",	civId: "metallokopta",	x: 572,	y: 538,	env: "metallic",	influence: 128,	radius: 6577,	temp: -181,	atmos: "co2",	orbit: 9.46,	prod:{ caesium: 2.1, thorium: 1.2, osmium: 3.1, iron: 8.7, titanium: 5.7, uranium: 13.4, rhodium: 2.6 },	},
    {	id: "xora",	nebulaId: "perseus",	civId: "metallokopta",	x: 715,	y: 556,	env: "metallic",	influence: 173,	radius: 8230,	temp: -58,	atmos: "methane",	orbit: 7.45,	prod:{ thorium: .8, osmium: 5, methane: 2.2, iron: 4.1, titanium: 9.5, uranium: 7.2, silicon: 5.2, rhodium: 8.7 },	},
    {	id: "babilo",	nebulaId: "perseus",	civId: "orion",	x: 267,	y: 158,	env: "ice",	influence: 650,	radius: 8230,	temp: -58,	atmos: "co2",	orbit: 7.45,	prod:{ nanotube: 5, ice: 3, water: .8, iron: 4, titanium: 3.5, uranium: 2.2, robot: 2, osmium: 3 },	},    
    {	id: "nassaus",	nebulaId: "perseus",	civId: "pirates",	x: 80,	y: 300,	env: "lava",	influence: 38,	radius: 6771,	temp: 582,	atmos: "sulfur",	orbit: .1,	prod: { thorium: 1.5, rhodium: 1.8, graphite: 3.4, sulfur: 1, titanium: 5 },	},

    {	id: "solidad",	nebulaId: "andromeda",	civId: null,	x: 473,	y: 20,	env: "terrestrial",	influence: 5,	radius: 5741,	temp: 38,	atmos: "oxygen",	orbit: .85,	prod: { iron: 3, graphite: 2, titanium: 3, methane: 2, oil: 1.5, water: .5 },	},
    {	id: "conquest",	nebulaId: "andromeda",	civId: "wahrian",	x: 537,	y: 98,	env: "ice",	influence: 900,	radius: 3220,	temp: -158,	atmos: "co2",	orbit: 3,	prod: { coolant: 1.5, ice: 5, titanium: 1, osmium: 2.8, uranium: 1.5, graphite: 1.6 },	},
    {	id: "kartarid",	nebulaId: "andromeda",	civId: "wahrian",	x: 475,	y: 149,	env: "ice",	influence: 1850,	radius: 2891,	temp: -171,	atmos: "co2",	orbit: 8,	prod: { coolant: 2, ice: 2.5, titanium: 3.5, uranium: 4, iron: 8, methane: 1.8 },	},
    {	id: "cerberus",	nebulaId: "andromeda",	civId: "wahrian",	x: 383,	y: 162,	env: "lava",	influence: 2498,	radius: 4230,	temp: 322,	atmos: "sulfur",	orbit: .45,	prod: { armor: 3, engine: 3, thorium: 4, sulfur: 2.5, graphite: 1.7, titanium: 5, rhodium: .5 },	},
    {	id: "death",	nebulaId: "andromeda",	civId: "wahrian",	x: 519,	y: 211,	env: "lava",	influence: 3132,	radius: 5661,	temp: 591,	atmos: "sulfur",	orbit: .25,	prod: { sulfur: 2.7, thorium: 2, rhodium: 3, graphite: 3.5, titanium: 1.2, engine: 5 },	},
    {	id: "yanyin",	nebulaId: "andromeda",	civId: "juini",	x: 708,	y: 163,	env: "ocean",	influence: 3844,	radius: 12242,	temp: 31,	atmos: "hydrogen",	orbit: .8,	prod: { sand: 1.5, titanium: .5, oil: 1, water: 3, graphite: 3, rhodium: 11 },	},
    {	id: "siris",	nebulaId: "andromeda",	civId: "juini",	x: 789,	y: 86,	env: "ocean",	influence: 5222,	radius: 15180,	temp: 6,	atmos: "hydrogen",	orbit: 1.7,	prod: { sand: 2.5, uranium: .8, oil: 1.2, water: 1.5, graphite: 2.8, osmium: 7.1 },	},
    {	id: "xilea",	nebulaId: "andromeda",	civId: "juini",	x: 928,	y: 70,	env: "ocean",	influence: 6920,	radius: 15705,	temp: 19,	atmos: "hydrogen",	orbit: 1.12,	prod: { caesium: .5, sand: .5, uranium: 3.1, titanium: 2.2, water: 5, graphite: 1 },	},
    {	id: "asun",	nebulaId: "andromeda",	civId: "juini",	x: 877,	y: 181,	env: "ocean",	influence: 15520,	radius: 13010,	temp: 3,	atmos: "hydrogen",	orbit: 2.04,	prod: { sand: 1, iron: 2, uranium: 5.7, titanium: 2, water: 12 },	},
    {	id: "swamp",	nebulaId: "andromeda",	civId: "andromeda",	x: 549,	y: 293,	env: "terrestrial",	influence: 4711,	radius: 5390,	temp: 21,	atmos: "oxygen",	orbit: .72,	prod: { sand: 1.5, titanium: .5, oil: 1, water: 3, graphite: 3 },	},
    {	id: "columbus",	nebulaId: "andromeda",	civId: "andromeda",	x: 443,	y: 342,	env: "radioactive",	influence: 5898,	radius: 8004,	temp: -26,	atmos: "co2",	orbit: 1.5,	prod: { thorium: 1.8, iron: 2, graphite: 5.1, uranium: 15.6, osmium: 2.7 },	},
    {	id: "magellan",	nebulaId: "andromeda",	civId: "andromeda",	x: 527,	y: 387,	env: "acid",	influence: 8004,	radius: 4532,	temp: 370,	atmos: "acid",	orbit: .6,	prod: { meissnerium: 1.8, caesium: 1, titanium: 2.5, graphite: 3, sulfur: 5 },	},
    {	id: "gerlache",	nebulaId: "andromeda",	civId: "andromeda",	x: 663,	y: 366,	env: "ice",	influence: 11422,	radius: 2801,	temp: -71,	atmos: "oxygen",	orbit: 2.9,	prod: { ice: 2.9, thorium: 3, sand: 1.5, titanium: .5, oil: 1, water: 3, graphite: 3 },	},
    {	id: "gagarin",	nebulaId: "andromeda",	civId: "andromeda",	x: 683,	y: 441,	env: "terrestrial",	influence: 21870,	radius: 7268,	temp: 15,	atmos: "oxygen",	orbit: .5,	prod: { darkmatter: 2.2, sand: 1.5, titanium: .5, oil: 1, water: 1, graphite: 3 },	},
    {	id: "alfari",	nebulaId: "andromeda",	civId: "karan",	x: 262,	y: 52,	env: "desert",	influence: 580,	radius: 8256,	temp: 68,	atmos: "co2",	orbit: .7,	prod: { ammunition: 2, sand: 5, titanium: 15.5, silicon: 3, oil: 3, water: 1, graphite: 8 },	},
    {	id: "xeno",	nebulaId: "andromeda",	civId: "karan",	x: 164,	y: 32,	env: "radioactive",	influence: 808,	radius: 4009,	temp: -101,	atmos: "co2",	orbit: 11.81,	prod: { uammunition: 2, thorium: 1.8, titanium: 8, graphite: 11, uranium: 7, rhodium: 4.8, osmium: 3.5 },	},
    {	id: "caligo",	nebulaId: "andromeda",	civId: "karan",	x: 66,	y: 59,	env: "acid",	influence: 32740,	radius: 6208,	temp: 322,	atmos: "acid",	orbit: .52,	prod: { meissnerium: 1.5, caesium: 1.5, sulfur: 6.2, titanium: 3, graphite: 2, rhodium: 7 },	},
    {	id: "halea",	nebulaId: "andromeda",	civId: "juinika",	x: 803,	y: 248,	env: "ocean",	influence: 9e4,	radius: 9889,	temp: 22,	atmos: "hydrogen",	orbit: .8,	prod: { titanium: 18, graphite: 15, sand: 15, water: 18 },	},
    {	id: "persephone",	nebulaId: "andromeda",	civId: "arcadia",	x: 277,	y: 208,	env: "terrestrial",	influence: 18e3,	radius: 5366,	temp: 25,	atmos: "oxygen",	orbit: 1.1,	prod: { titanium: 18, graphite: 15, sand: 15, water: 18 },	},
    {	id: "hades",	nebulaId: "andromeda",	civId: "arcadia",	x: 147,	y: 179,	env: "desert",	influence: 44e3,	radius: 2251,	temp: 41,	atmos: "oxygen",	orbit: .9,	prod: { darkmatter: 1.8, titanium: 18, graphite: 15, sand: 15, water: 18 },	},
    {	id: "demeter",	nebulaId: "andromeda",	civId: "arcadia",	x: 66,	y: 217,	env: "gas",	influence: 15e4,	radius: 89044,	temp: 422,	atmos: "helium",	orbit: .1,	prod: { darkmatter: 2.5, hydrogen: 6, methane: 54, technetium: 8 },	},
    {	id: "hermr",	nebulaId: "andromeda",	civId: "yolur",	x: 246,	y: 248,	env: "acid",	influence: 58e3,	radius: 2007,	temp: 282,	atmos: "co2",	orbit: 4,	prod: { thorium: 2.5, sulfur: 3.4, titanium: 3, engine: 5 },	},
    {	id: "calipsi",	nebulaId: "andromeda",	civId: "yolur",	x: 172,	y: 309,	env: "ammonia",	influence: 98e3,	radius: 3015,	temp: -58,	atmos: "nitrogen",	orbit: 5.6,	prod: { ammonia: 6.2, titanium: 2.5, uranium: 1.1, coolant: 5 },	},
    {	id: "auriga",	nebulaId: "andromeda",	civId: "yolur",	x: 262,	y: 272,	env: "ammonia",	influence: 6e4,	radius: 3847,	temp: -64,	atmos: "nitrogen",	orbit: 8.5,	prod: { ammonia: 12, uranium: 5 },	},
    {	id: "cygnus",	nebulaId: "andromeda",	civId: "yolur",	x: 205,	y: 402,	env: "ammonia",	influence: 164e3,	radius: 4382,	temp: -72,	atmos: "nitrogen",	orbit: 5.2,	prod: { darkmatter: 2, ammonia: 15.8 },	},
    {	id: "forax",	nebulaId: "andromeda",	civId: "yolur",	x: 18,	y: 365,	env: "carbon",	influence: 152e3,	radius: 3766,	temp: -22,	atmos: "nitrogen",	orbit: 3,	prod: { graphite: 25, titanium: 8, thorium: 4.5, nanotube: 55 },	},
    {	id: "volor",	nebulaId: "andromeda",	civId: "yolur",	x: 188,	y: 507,	env: "carbon",	influence: 25e4,	radius: 2541,	temp: -28,	atmos: "nitrogen",	orbit: 6.1,	prod: { titanium: 12, graphite: 15, thorium: 7.1, nanotube: 135 },	},
													
    {	id: "xirandrus",	nebulaId: "void",	civId: "protokopta",	x: 10,	y: 136,	env: "terrestrial",	influence: 88e3,	radius: 5974,	temp: 21,	atmos: "oxygen",	orbit: .5,	prod: { steel: .1, iron: .8, titanium: 14.3, graphite: 9.4, oil: .8, water: 4.1, sand: .1 },	},
    {	id: "peleuvis",	nebulaId: "void",	civId: "darkarmy",	x: 314,	y: 565,	env: "ocean",	influence: 7e5,	radius: 14179,	temp: 2,	atmos: "hydrogen",	orbit: 2.1,	prod: { iron: .6, graphite: 10.9, water: 1.2, sand: 13.1 },	},
    {	id: "cranium",	nebulaId: "void",	civId: "darkarmy",	x: 328,	y: 265,	env: "lava",	influence: 46e4,	radius: 4572,	temp: 203,	atmos: "sulfur",	orbit: 4,	prod: { steel: .1, titanium: 4.7, graphite: .2, rhodium: 5.6, ice: 2.3, sulfur: 1, thorium: 3.9 },	},
    {	id: "exabolan",	nebulaId: "void",	civId: "darkarmy",	x: 420,	y: 509,	env: "radioactive",	influence: 5e5,	radius: 7301,	temp: -48,	atmos: "co2",	orbit: 7.2,	prod: { steel: .2, graphite: 4.7, osmium: 2.2, rhodium: 1.2, uranium: 14.1, thorium: .2 },	},
    {	id: "madame",	nebulaId: "void",	civId: "darkarmy",	x: 292,	y: 140,	env: "carbon",	influence: 3e5,	radius: 2732,	temp: -28,	atmos: "nitrogen",	orbit: 4.4,	prod: { darkmatter: 1.3, titanium: 9, graphite: 7.2, thorium: 1 },	},
    {	id: "elon",	nebulaId: "void",	civId: "wardens",	x: 599,	y: 459,	env: "ocean",	influence: 8e5,	radius: 14842,	temp: 20,	atmos: "hydrogen",	orbit: .9,	prod: { nanotube: .5, titanium: 14.8, oil: .8, water: 4.9, sand: 2.8 },	},
    {	id: "gora",	nebulaId: "void",	civId: "darkarmy",	x: 471,	y: 388,	env: "desert",	influence: 7e5,	radius: 5171,	temp: 99,	atmos: "co2",	orbit: .5,	prod: { titanium: 1.6, oil: 2.2, water: 5.2, rhodium: 1.9, uranium: 1.8, sand: 11.5 },	},
    {	id: "yllirium",	nebulaId: "void",	civId: "wardens",	x: 553,	y: 554,	env: "ice",	influence: 9e5,	radius: 3367,	temp: -27,	atmos: "co2",	orbit: 4.4,	prod: { meissnercell: 2, iron: 4.9, titanium: .4, methane: .6, uranium: 2.9, ice: .3 },	},
    {	id: "malus",	nebulaId: "void",	civId: "darkarmy",	x: 606,	y: 258,	env: "acid",	influence: 8e5,	radius: 3932,	temp: 313,	atmos: "co2",	orbit: 2.9,	prod: { titanium: .2, rhodium: 5.2, sulfur: 2.7, caesium: .9 },	},
    {	id: "janus",	nebulaId: "void",	civId: "wardens",	x: 343,	y: 395,	env: "gas",	influence: 85e4,	radius: 80462,	temp: -103,	atmos: "hydrogen",	orbit: 7.1,	prod: { technetium: 8, antimatter: 12, hydrogen: 76.5, methane: 37.5, caesium: 2.2 },	},
    {	id: "aquarius",	nebulaId: "void",	civId: "darkarmy",	x: 397,	y: 73,	env: "ammonia",	influence: 6e5,	radius: 3446,	temp: -60,	atmos: "nitrogen",	orbit: 7.6,	prod: { uranium: 2.4, ammonia: 12 },	},
    {	id: "japheth",	nebulaId: "void",	civId: "wardens",	x: 569,	y: 340,	env: "gas",	influence: 8e5,	radius: 31706,	temp: -98,	atmos: "hydrogen",	orbit: 4.7,	prod: { technetium: 7, antimatter: 8, hydrogen: 42.6, methane: 17.4 },	},
    {	id: "poligon",	nebulaId: "void",	civId: "wardens",	x: 740,	y: 392,	env: "gas",	influence: 8e5,	radius: 42666,	temp: -109,	atmos: "hydrogen",	orbit: 3.8,	prod: { technetium: 9, antimatter: 3, hydrogen: 23.5, methane: 31.1 },	},
    {	id: "atlas",	nebulaId: "void",	civId: "protokopta",	x: 168,	y: 0,	env: "gas",	influence: 302e3,	radius: 44368,	temp: -141,	atmos: "hydrogen",	orbit: 9.7,	prod: { darkmatter: 1.5, technetium: 16, antimatter: 11, hydrogen: 30.6, methane: 40.7 },	},
    {	id: "augmeris",	nebulaId: "void",	civId: "protokopta",	x: 50,	y: 30,	env: "radioactive",	influence: 44e4,	radius: 7666,	temp: -37,	atmos: "co2",	orbit: 9.2,	prod: { steel: .25, iron: .2, graphite: 8.7, osmium: 1.2, uranium: 12.8, thorium: .6 },	},
    {	id: "cetus",	nebulaId: "void",	civId: "wardens",	x: 402,	y: 191,	env: "ocean",	influence: 52e4,	radius: 13479,	temp: 3,	atmos: "co2",	orbit: 2.4,	prod: { titanium: 11.4, graphite: .1, oil: .8, water: 4.6, osmium: 5, sand: .6 },	},
    {	id: "erpervestalis",	nebulaId: "void",	civId: "wardens",	x: 794,	y: 313,	env: "acid",	influence: 9e5,	radius: 5098,	temp: 284,	atmos: "acid",	orbit: 1.1,	prod: { meissnerium: 3, titanium: .3, graphite: .9, sulfur: 5.9 },	},
    {	id: "etaaras",	nebulaId: "void",	civId: "wardens",	x: 553,	y: 35,	env: "ammonia",	influence: 7e5,	radius: 3905,	temp: -66,	atmos: "nitrogen",	orbit: 5.9,	prod: { ammonia: 1.7 },	},
    {	id: "jardin",	nebulaId: "void",	civId: "wardens",	x: 694,	y: 522,	env: "ammonia",	influence: 1e6,	radius: 4218,	temp: -62,	atmos: "nitrogen",	orbit: 5.6,	prod: { ammonia: 7.9 },	},
    {	id: "karmirion",	nebulaId: "void",	civId: "darkarmy",	x: 896,	y: 444,	env: "carbon",	influence: 9e5,	radius: 3048,	temp: -27,	atmos: "nitrogen",	orbit: 4.3,	prod: { titanium: 6.4, graphite: 12.6, thorium: 3.4 },	},
    {	id: "parai",	nebulaId: "void",	civId: "wardens",	x: 824,	y: 523,	env: "terrestrial",	influence: 15e5,	radius: 5679,	temp: 24,	atmos: "oxygen",	orbit: .7,	prod: { iron: .7, titanium: 16.7, graphite: 2.4, oil: .4, water: 12.7, sand: 10.6 },	},
    {	id: "premeza",	nebulaId: "void",	civId: "wardens",	x: 641,	y: 91,	env: "terrestrial",	influence: 7e5,	radius: 5468,	temp: 37,	atmos: "oxygen",	orbit: .6,	prod: { steel: .3, titanium: 13.8, graphite: 14.7, oil: 1.5, water: 3.3 },	},
    {	id: "unia",	nebulaId: "void",	civId: "darkarmy",	x: 865,	y: 196,	env: "radioactive",	influence: 15e5,	radius: 6536,	temp: -33,	atmos: "co2",	orbit: 5.8,	prod: { iron: .8, titanium: 6.2, graphite: 7.3, osmium: 2.5, rhodium: .3, uranium: 6.6, thorium: .8 },	},
    {	id: "vanubis",	nebulaId: "void",	civId: "protokopta",	x: 396,	y: 0,	env: "metallic",	influence: 4e5,	radius: 3405,	temp: 2,	atmos: "co2",	orbit: 4.5,	prod: { steel: .5, iron: 8.4, titanium: .9, methane: .5, rhodium: 6.7, uranium: 6.3, caesium: 1.9, thorium: .9 },	},
    {	id: "vikasuka",	nebulaId: "void",	civId: "darkarmy",	x: 546,	y: 191,	env: "metallic",	influence: 7e5,	radius: 10730,	temp: -69,	atmos: "methane",	orbit: 5,	prod: { iron: .2, titanium: 6.7, methane: 1.9, rhodium: 4.5, uranium: 8.4, caesium: .8 },	},
    {	id: "discordia",	nebulaId: "void",	civId: "darkarmy",	x: 202,	y: 168,	env: "ice",	influence: 103e3,	radius: 5047,	temp: -20,	atmos: "co2",	orbit: 5.1,	prod: { meissnerium: 2, iron: 7, titanium: .1, methane: .9, water: 1, rhodium: 2.1, uranium: 1.5, ice: 1.7 },	},
    {	id: "mallus",	nebulaId: "void",	civId: "protokopta",	x: 266,	y: 54,	env: "ammonia",	influence: 105e3,	radius: 4059,	temp: -62,	atmos: "nitrogen",	orbit: 5.3,	prod: { tammunition: 2, technetium: 4, ammonia: 14.7 },	},
    {	id: "lascura",	nebulaId: "void",	civId: "wardens",	x: 66,	y: 301,	env: "ocean",	influence: 23e4,	radius: 8397,	temp: 10,	atmos: "hydrogen",	orbit: 1.5,	prod: { caesium: 5.5, graphite: 6.7, oil: 1.1, water: 7.7, sand: 5.1 },	},
    {	id: "pollux",	nebulaId: "void",	civId: "darkarmy",	x: 250,	y: 302,	env: "metallic",	influence: 28e4,	radius: 8720,	temp: -49,	atmos: "methane",	orbit: 2.9,	prod: { iron: 3.7, titanium: 1.7, methane: .7, rhodium: 3.7, uranium: 4.3, thorium: .7, superconductor: 2 },	},
    {	id: "mihandria",	nebulaId: "void",	civId: "wardens",	x: 49,	y: 399,	env: "terrestrial",	influence: 213e3,	radius: 3405,	temp: 35,	atmos: "oxygen",	orbit: .6,	prod: { iron: .3, titanium: 11.6, graphite: 3.4, oil: .4, methane: 1.1, water: 6.3, sand: 14.7 },	},
    {	id: "mellivor",	nebulaId: "void",	civId: "darkarmy",	x: 154,	y: 345,	env: "carbon",	influence: 5e5,	radius: 2996,	temp: -27,	atmos: "nitrogen",	orbit: 4.6,	prod: { nanotube: 96, titanium: 3.4, graphite: 5.1, thorium: 1.9 },	},
    {	id: "extremandur",	nebulaId: "void",	civId: "darkarmy",	x: 151,	y: 437,	env: "desert",	influence: 25e4,	radius: 2931,	temp: 81,	atmos: "co2",	orbit: 1.1,	prod: { iron: 3.6, titanium: .9, rhodium: 2, uranium: 4.8, sand: 11.9 },	},
    {	id: "urdum",	nebulaId: "void",	civId: "wardens",	x: 256,	y: 379,	env: "ammonia",	influence: 52e4,	radius: 4255,	temp: -72,	atmos: "nitrogen",	orbit: 7.7,	prod: { coolant: 5, ammonia: 13.9 },	},
    {	id: "hordron",	nebulaId: "void",	civId: "wardens",	x: 299,	y: 490,	env: "gas",	influence: 75e4,	radius: 88664,	temp: -135,	atmos: "hydrogen",	orbit: 11.2,	prod: { technetium: 15, hydrogen: 15.3, methane: 49.2 },	},
    {	id: "viscarius",	nebulaId: "void",	civId: "darkarmy",	x: 178,	y: 527,	env: "desert",	influence: 5e5,	radius: 8641,	temp: 65,	atmos: "co2",	orbit: 1.1,	prod: { titanium: 3.9, graphite: 5.6, rhodium: 2, uranium: 4.4, sand: 4.6 },	},
    {	id: "bolmir",	nebulaId: "void",	civId: "wardens",	x: 86,	y: 218,	env: "acid",	influence: 208e3,	radius: 3960,	temp: 285,	atmos: "co2",	orbit: 3.4,	prod: { titanium: .5, sulfur: 1.6, caesium: .4, thorium: .9 },	},
    {	id: "misfir",	nebulaId: "void",	civId: "wardens",	x: 10,	y: 484,	env: "ice",	influence: 5e5,	radius: 4262,	temp: -39,	atmos: "co2",	orbit: 7.2,	prod: { meissnercell: 2, iron: 6.8, titanium: 3.1, ice: 3.3, thorium: .4 },	},
    {	id: "vehemir",	nebulaId: "void",	civId: "darkarmy",	x: 75,	y: 558,	env: "lava",	influence: 48e4,	radius: 2493,	temp: 183,	atmos: "sulfur",	orbit: 2.5,	prod: { rhodium: 6.6, sulfur: 2.2, thorium: 1 },	},
]

var routeRefs = [

    {	pl1Id: "promision",	pl2Id: "vasilis", },
    {	pl1Id: "vasilis",	pl2Id: "aequoreas", },
    {	pl1Id: "aequoreas",	pl2Id: "orpheus", },
    {	pl1Id: "orpheus",	pl2Id: "city", },
    {	pl1Id: "orpheus",	pl2Id: "ishtar", },
    {	pl1Id: "orpheus",	pl2Id: "antirion", },
    {	pl1Id: "posirion",	pl2Id: "zelera", },
    {	pl1Id: "ishtar",	pl2Id: "tataridu", },
    {	pl1Id: "ishtar",	pl2Id: "traumland", },
    {	pl1Id: "traumland",	pl2Id: "zelera", },
    {	pl1Id: "ishtar",	pl2Id:"acanthus", },
    {	pl1Id: "raknar",	pl2Id: "acanthus", },
    {	pl1Id: "acanthus",	pl2Id: "jabir", },
    {	pl1Id: "jabir",	pl2Id: "teleras", },
    {	pl1Id: "antaris",	pl2Id: "jabir", },
    {	pl1Id: "zhura",	pl2Id: "bharash", },
    {	pl1Id: "epsilon",	pl2Id: "zhura", },
    {	pl1Id: "caerul",	pl2Id: "epsilon", },
    {	pl1Id: "epsilon",	pl2Id: "phorun", },
    {	pl1Id: "posirion",	pl2Id: "phorun", },
    {	pl1Id: "antaris",	pl2Id: "tsartasis", },
    {	pl1Id: "ares",	pl2Id: "kandi", },
    {	pl1Id: "kandi",	pl2Id: "shin", },
    {	pl1Id: "shin",	pl2Id: "tsartasis", },
    {	pl1Id: "shin",	pl2Id: "xora2", },
    {	pl1Id: "xora2",	pl2Id: "xora", },
    {	pl1Id: "tsartasis",	pl2Id: "mermorra", },
    {	pl1Id: "kitrion",	pl2Id: "mermorra", },
    {	pl1Id: "ishtar",	pl2Id: "babilo", },
    {	pl1Id: "raknar",	pl2Id: "nassaus", },

    {	pl1Id: "solidad",	pl2Id: "conquest", },
    {	pl1Id: "conquest",	pl2Id: "kartarid", },
    {	pl1Id: "kartarid",	pl2Id: "cerberus", },
    {	pl1Id: "kartarid",	pl2Id: "death", },
    {	pl1Id: "death",	pl2Id: "yanyin", },
    {	pl1Id: "death",	pl2Id: "swamp", },
    {	pl1Id: "swamp",	pl2Id: "columbus", },
    {	pl1Id: "columbus",	pl2Id: "magellan", },
    {	pl1Id: "magellan",	pl2Id: "gerlache", },
    {	pl1Id: "gerlache",	pl2Id: "gagarin", },
    {	pl1Id: "yanyin",	pl2Id: "siris", },
    {	pl1Id: "siris",	pl2Id: "xilea", },
    {	pl1Id: "xilea",	pl2Id: "asun", },
    {	pl1Id: "solidad",	pl2Id: "alfari", },
    {	pl1Id: "xeno",	pl2Id: "alfari", },
    {	pl1Id: "xeno",	pl2Id: "caligo", },
    {	pl1Id: "asun",	pl2Id: "halea", },
    {	pl1Id: "cerberus",	pl2Id: "persephone", },
    {	pl1Id: "hades",	pl2Id: "persephone", },
    {	pl1Id: "demeter",	pl2Id: "hades", },
    {	pl1Id: "persephone",	pl2Id: "hermr", },
    {	pl1Id: "hermr",	pl2Id: "auriga", },
    {	pl1Id: "hermr",	pl2Id: "calipsi", },
    {	pl1Id: "calipsi",	pl2Id: "cygnus", },
    {	pl1Id: "calipsi",	pl2Id: "forax", },
    {	pl1Id: "cygnus",	pl2Id: "volor", },
    
    {	pl1Id: "xirandrus",	pl2Id: "discordia", },
    {	pl1Id: "xirandrus",	pl2Id: "mallus", },
    {	pl1Id: "xirandrus",	pl2Id: "lascura", },
    {	pl1Id: "discordia",	pl2Id: "pollux", },
    {	pl1Id: "lascura",	pl2Id: "mihandria", },
    {	pl1Id: "pollux",	pl2Id: "mellivor", },
    {	pl1Id: "lascura",	pl2Id: "extremandur", },
    {	pl1Id: "pollux",	pl2Id: "urdum", },
    {	pl1Id: "urdum",	pl2Id: "hordron", },
    {	pl1Id: "extremandur",	pl2Id: "viscarius", },
    {	pl1Id: "discordia",	pl2Id: "bolmir", },
    {	pl1Id: "misfir",	pl2Id: "mihandria", },
    {	pl1Id: "mihandria",	pl2Id: "vehemir", },
    {	pl1Id: "vehemir",	pl2Id: "peleuvis", },
    {	pl1Id: "pollux",	pl2Id: "cranium", },
    {	pl1Id: "viscarius",	pl2Id: "exabolan", },
    {	pl1Id: "discordia",	pl2Id: "madame", },
    {	pl1Id: "exabolan",	pl2Id: "elon", },
    {	pl1Id: "cranium",	pl2Id: "gora", },
    {	pl1Id: "peleuvis",	pl2Id: "yllirium", },
    {	pl1Id: "cranium",	pl2Id: "malus", },
    {	pl1Id: "janus",	pl2Id: "exabolan", },
    {	pl1Id: "madame",	pl2Id: "aquarius", },
    {	pl1Id: "gora",	pl2Id: "japheth", },
    {	pl1Id: "gora",	pl2Id: "poligon", },
    {	pl1Id: "atlas",	pl2Id: "mallus", },
    {	pl1Id: "atlas",	pl2Id: "augmeris", },
    {	pl1Id: "madame",	pl2Id: "cetus", },
    {	pl1Id: "japheth",	pl2Id: "erpervestalis", },
    {	pl1Id: "aquarius",	pl2Id: "etaaras", },
    {	pl1Id: "poligon",	pl2Id: "jardin", },
    {	pl1Id: "poligon",	pl2Id: "karmirion", },
    {	pl1Id: "karmirion",	pl2Id: "parai", },
    {	pl1Id: "aquarius",	pl2Id: "premeza", },
    {	pl1Id: "erpervestalis",	pl2Id: "unia", },
    {	pl1Id: "atlas",	pl2Id: "vanubis", },
    {	pl1Id: "cetus",	pl2Id: "vikasuka", },
]

var fleetRefs = [

    {	civId: "city",	name: "The Keeper",	planetId: "city",	experience: 6,	ships: { ark22: 100, ark55: 50, foxar: 35, babayaga: 10, glassBurson: 3, key: 1 },	},
    
    {	civId: "phantids",	name: "Phantids Defence Fleet",	planetId: "traumland",	experience: 12,	ships: { mastodon: 1 },	},
    {	civId: "phantids",	name: "Vernichtung",	planetId: "tataridu",	experience: 18,	ships: { dieSchoene: 1, alptraum: 35, engel: 500 },	},														
    
    {	civId: "metallokopta",	name: "Thlipsi Fleet",	planetId: "tsartasis",	experience: 0,	ships: { swarm: 8e3 , heartSwarm: 1 },	},
    {	civId: "metallokopta",	name: "Monaxia Fleet",	planetId: "mermorra",	experience: 80,	ships: { swarm: 8e4 , heartSwarm: 1 },	},
    {	civId: "metallokopta",	name: "Erimosi Fleet",	planetId: "shin",	experience: 0,	ships: { swarm: 8e4, enslavedHuman: 1e3, heartSwarm: 1 },	},
    {	civId: "metallokopta",	name: "Katastrofis Fleet",	planetId: "kitrion",	experience: 0,	ships: { swarm: 8e4, enslavedHuman: 2e3, heartSwarm: 1 },	},
    {	civId: "metallokopta",	name: "Loimos Fleet",	planetId: "kandi",	experience: 0,	ships: { swarm: 3e5, enslavedHuman: 2e3, heartSwarm: 10 },	},
    {	civId: "metallokopta",	name: "Polemos Fleet",	planetId: "ares",	experience: 80,	ships: { swarm: 8e5, enslavedHuman: 5e3, enslavedQuris: 1e3, heartSwarm: 20 },	},
    {	civId: "metallokopta",	name: "Thanatos Fleet",	planetId: "xora2",	experience: 100,	ships: { swarm: 3e6, enslavedHuman: 8e3, enslavedQuris: 5e3, enslavedHalean: 1e3, heartSwarm: 25 },	},
    {	civId: "metallokopta",	name: "Anastasi Fleet",	planetId: "xora",	experience: 150,	ships: { swarm: 1e7, enslavedHuman: 15e3, enslavedQuris: 8e3, cerberus: 2e3, enslavedHalean: 50, aureaPina: 1 },	},

    {	civId: "halean",	name: "Azure Fleet",	planetId: "caerul",	experience: 80,	ships: { haleanSpear: 5e4, haleanCounselor: 5e3, siren: 2e3, azureHuang: 5 },	},														

    {	civId: "quris",	name: "Purification Fleet",	planetId: "acanthus",	experience: 0,	ships: { auxilia: 1e3, },	},														
    {	civId: "quris",	name: "Konquista",	planetId: "raknar",	experience: 0,	ships: { auxilia: 3e3, },	},														
    {	civId: "quris",	name: "The Last Stand",	planetId: "antaris",	experience: 0,	ships: { auxilia: 5e3, },	},														
    {	civId: "quris",	name: "Styx Legion",	planetId: "teleras",	experience: 50,	ships: { auxilia: 1e4, },	},														
    {	civId: "quris",	name: "Hell Warden",	planetId: "jabir",	experience: 50,	ships: { auxilia: 3e3, deadSoul: 100, lucifer: 1 },	},														

    {	civId: "robot",	name: "I.R.C. S.E.A.S. F.L.E.E.T.",	planetId: "zelera",	experience: 22,	ships: { natsumiko: 1, harumiko: 1, akimiko: 1, fuyumiko: 1 },	},														

    {	civId: "pirates",	name: "The Ugly",	planetId: "antirion",	experience: 0,	ships: { arkprp: 30, tucoRamirez: 1 },	},														
    {	civId: "pirates",	name: "The Bad",	planetId: "antirion",	experience: 0,	ships: { ark55b: 150, angelEyes: 1 },	},														
    {	civId: "pirates",	name: "The Good",	planetId: "antirion",	experience: 0,	ships: { arkprp: 50, noName: 1 },	},														
    {	civId: "pirates",	name: "Silver Fleet",	planetId: "nassaus",	experience: 0,	ships: { blackStar: 1 },	},														
    
    {	civId: "orion",	name: "O.L. Defence Fleet",	planetId: "ishtar",	experience: 0,	ships: { aurora: 1 },	},
    {	civId: "orion",	name: "Babilo Protector",	planetId: "babilo",	experience: 100,	ships: { marduk: 1 },	},

    {	civId: "wahrian",	name: "Abiha",	planetId: "conquest",	experience: 0,	ships: { cherub: 1e7, zion : 1 },	},
    {	civId: "wahrian",	name: "Deborha",	planetId: "kartarid",	experience: 0,	ships: { cherub: 35e6, sodom : 1 },	},
    {	civId: "wahrian",	name: "Jerusha",	planetId: "cerberus",	experience: 0,	ships: { cherub: 5e7, seraph: 1e6, jericho: 1 },	},
    {	civId: "wahrian",	name: "Juditha",	planetId: "death",	experience: 200,	ships: { cherub: 5e7, seraph: 25e5, gomorrah: 1 },	},

    {	civId: "juini",	name: "Arjini Lisis Fleet",	planetId: "yanyin",	experience: 0,	ships: { siren: 10e6, aster: 1 },	},														
    {	civId: "juini",	name: "Aurin Firmis Fleet",	planetId: "siris",	experience: 0,	ships: { siren: 30e6, juiniDaughter: 1e6, azalea: 1 },	},														
    {	civId: "juini",	name: "Rubian Passis Fleet",	planetId: "xilea",	experience: 0,	ships: { siren: 100e6, juiniDaughter: 3e6, azureHuang: 1e6, dahlia: 1 },	},														
    {	civId: "juini",	name: "Safir Voluptua Fleet",	planetId: "asun",	experience: 300,	ships: { siren: 300e6, juiniDaughter: 10e6, azureHuang: 3e6, freesia: 1 },	},														

    {	civId: "andromeda",	name: "Esperanza Fleet",	planetId: "swamp",	experience: 0,	ships: { castilla: 10e6, salantara: 10 },	},
    {	civId: "andromeda",	name: "Antilla Fleet",	planetId: "columbus",	experience: 0,	ships: { castilla: 50e6, devil: 1e6, bellerophon: 10 },	},
    {	civId: "andromeda",	name: "Molucca Fleet",	planetId: "magellan",	experience: 0,	ships: { castilla: 100e6, devil: 3e6, wingsPegasus: 5 },	},
    {	civId: "andromeda",	name: "Australis Fleet",	planetId: "gerlache",	experience: 0,	ships: { castilla: 120e6, devil: 5e6, salantara: 25, wingsPegasus: 10 },	},
    {	civId: "andromeda",	name: "Astris Fleet",	planetId: "gagarin",	experience: 800,	ships: { castilla: 150e6, devil: 10e6, salantara: 50, bellerophon: 20, wingsPegasus: 15 },	},														

    {	civId: "karan",	name: "Canchrena Uxor",	planetId: "alfari",	experience: 0,	ships: { nux: 1e6, max: 35e4 },	},
    {	civId: "karan",	name: "Purulis Uxor",	planetId: "alfari",	experience: 0,	ships: { nux: 1e6, furiosa: 1e6 },	},
    {	civId: "karan",	name: "Alfari Uxor",	planetId: "alfari",	experience: 0,	ships: { max: 1e6, furiosa: 25e4 },	},
    {	civId: "karan",	name: "Xenopraedo",	planetId: "xeno",	experience: 100,	ships: { nux: 3e6, max: 1e6, angharad: 1 },	},
    {	civId: "karan",	name: "Xenoterrent",	planetId: "xeno",	experience: 100,	ships: { max: 1e6, furiosa: 1e6, angharad: 2 },	},														
    {	civId: "karan",	name: "Flucta Fleet",	planetId: "caligo",	experience: 1e3,	ships: { nux: 1e9, max: 1e9, furiosa: 1e9, angharad: 1e3, ace: 1 },	},														

    {	civId: "juinika",	name: "Mairi's Wisdom",	planetId: "halea",	experience: 200,	ships: { aster: 3e3, azalea: 1e3, dahlia: 300, freesia: 100, sean: 1 },	},
    {	civId: "juinika",	name: "Suranis' Strength",	planetId: "halea",	experience: 200,	ships: { aster: 3e3, azalea: 1e3, dahlia: 300, freesia: 100, dion: 1 },	},
    {	civId: "juinika",	name: "Juini's Pride",	planetId: "halea",	experience: 200,	ships: { aster: 3e3, azalea: 1e3, dahlia: 300, freesia: 100, gradh: 1 },	},														

    {	civId: "arcadia",	name: "Thymos Fleet",	planetId: "persephone",	experience: 0,	ships: { castilla: 5E8, devil: 5E9, alecto: 1 },	},
    {	civId: "arcadia",	name: "Zilia Fleet",	planetId: "hades",	experience: 0,	ships: { castilla: 2E9, devil: 2E10, alecto: 1, maegera: 1 },	},
    {	civId: "arcadia",	name: "Fonos Fleet",	planetId: "demeter",	experience: 0,	ships: { castilla: 5E11, devil: 5E12, alecto: 1, maegera: 1, tisiphon: 1 },	},

    {	civId: "yolur",	name: "Formation Y331",	planetId: "hermr",	experience: 80,	ships: { lightMexager: 1 },	},
    {	civId: "yolur",	name: "Formation Y184",	planetId: "auriga",	experience: 120,	ships: { lightMexager: 2 },	},
    {	civId: "yolur",	name: "Formation Y67",	planetId: "calipsi",	experience: 200,	ships: { lightMexager: 5, lightCompanion: 2 },	},
    {	civId: "yolur",	name: "Formation Y22",	planetId: "forax",	experience: 500,	ships: { lightMexager: 5, lightCompanion: 2, vela: 1 },	},
    {	civId: "yolur",	name: "Formation Y06",	planetId: "cygnus",	experience: 800,	ships: { lightMexager: 100, lightCompanion: 20, vela: 1 },	},
    {	civId: "yolur",	name: "Formation Y01",	planetId: "volor",	experience: 1200,	ships: { lightMexager: 25, lightCompanion: 10, vela: 5 },	},
    
    {	civId: "darkarmy",	name: "Lord Aeshma's Fleet",	planetId: "madame",	experience: 0,	ships: { goghVan: 1110, matisse: 16390, kingNoir: 248832 },	},														    
    {	civId: "darkarmy",	name: "Lord Daeva's Fleet",	planetId: "aquarius",	experience: 0,	ships: { goya: 10956, goghVan: 1093500, matisse: 7091712, kingNoir: 144 },	},														
    {	civId: "darkarmy",	name: "Lord Charun's Fleet",	planetId: "vikasuka",	experience: 0,	ships: { munch: 243, goya: 1634782, goghVan: 2985984, matisse: 9938958, kingNoir: 9529488 },	},														
    {	civId: "darkarmy",	name: "Lord Saleos's Fleet",	planetId: "pollux",	experience: 0,	ships: { goghVan: 20, matisse: 11232, kingNoir: 2173392 },	},														
    {	civId: "darkarmy",	name: "Lord Raum's Fleet",	planetId: "mellivor",	experience: 0,	ships: { goya: 598, goghVan: 933120, matisse: 7091712, kingNoir: 9529488 },	},														
    {	civId: "darkarmy",	name: "Lord Vassago's Fleet",	planetId: "malus",	experience: 0,	ships: { munch: 15980, goya: 590976, goghVan: 7091712, matisse: 7091712, kingNoir: 2985984 },	},														
    {	civId: "darkarmy",	name: "Lord Namtar's Fleet",	planetId: "cranium",	experience: 0,	ships: { goya: 563, goghVan: 590976, matisse: 7091712, kingNoir: 2985984 },	},														
    {	civId: "darkarmy",	name: "Lord Malphas's Fleet",	planetId: "gora",	experience: 0,	ships: { munch: 540, goya: 291600, goghVan: 7091712, matisse: 9529488, kingNoir: 9529488 },	},														
    {	civId: "darkarmy",	name: "Lord Merihem's Fleet",	planetId: "karmirion",	experience: 0,	ships: { munch: 9631855, goya: 9992341, goghVan: 9529488, matisse: 9992341, kingNoir: 9938958 },	},														
    {	civId: "darkarmy",	name: "Lord Eisheth's Fleet",	planetId: "extremandur",	experience: 0,	ships: { goghVan: 36, matisse: 11714, kingNoir: 248832 },	},														
    {	civId: "darkarmy",	name: "Lord Beleth's Fleet",	planetId: "viscarius",	experience: 0,	ships: { goya: 540, goghVan: 334368, matisse: 9938958, kingNoir: 9529488 },	},														
    {	civId: "darkarmy",	name: "Lord Andras's Fleet",	planetId: "vehemir",	experience: 0,	ships: { goya: 34, goghVan: 641763, matisse: 7091712, kingNoir: 7091712 },	},														
    {	civId: "darkarmy",	name: "Lord Alastor's Fleet",	planetId: "peleuvis",	experience: 0,	ships: { munch: 1, goya: 248832, goghVan: 2985984, matisse: 2985984, kingNoir: 2985984 },	},														
    {	civId: "darkarmy",	name: "Lord Agares's Fleet",	planetId: "exabolan",	experience: 0,	ships: { goya: 13478, goghVan: 2985984, matisse: 7091712, kingNoir: 7091712 },	},														
    {	civId: "darkarmy",	name: "Lord Phenex's Fleet",	planetId: "discordia",	experience: 0,	ships: { goghVan: 4, matisse: 11714, kingNoir: 933120 },	},														
    {	civId: "darkarmy",	name: "Lord Orobas's Fleet",	planetId: "unia",	experience: 0,	ships: { darklordNest: 1, munch: 1e7, goya: 1e7, goghVan: 1e7, matisse: 1e7, kingNoir: 1e7 },	},														
    
    {	civId: "wardens",	name: "Lady Abathar's Fleet",	planetId: "etaaras",	experience: 0,	ships: { salvador: 363, ernst: 248832, magrit: 9529488, miro: 9529488 },	},														
    {	civId: "wardens",	name: "Lady Hashmal's Fleet",	planetId: "premeza",	experience: 0,	ships: { salvador: 243, ernst: 248832, magrit: 1728, miro: 248832 },	},														
    {	civId: "wardens",	name: "Lady Anael's Fleet",	planetId: "cetus",	experience: 0,	ships: { ernst: 540, magrit: 144, miro: 2985984 },	},														
    {	civId: "wardens",	name: "Lady Shamsiel's Fleet",	planetId: "bolmir",	experience: 0,	ships: { magrit: 6, miro: 10935 },	},														
    {	civId: "wardens",	name: "Lady Dumah's Fleet",	planetId: "lascura",	experience: 0,	ships: { magrit: 5, miro: 410 },	},														
    {	civId: "wardens",	name: "Lady Pheran's Fleet",	planetId: "urdum",	experience: 0,	ships: { ernst: 356, magrit: 334368, miro: 709e4 },	},														
    {	civId: "wardens",	name: "Lady Vehuel's Fleet",	planetId: "janus",	experience: 0,	ships: { salvador: 67598, ernst: 1e7, magrit: 1e7, miro: 1e7 },	},														
    {	civId: "wardens",	name: "Lady Hesediel's Fleet",	planetId: "erpervestalis",	experience: 0,	ships: { salvador: 3688760, ernst: 1e7, magrit: 1e7, miro: 1e7 },	},														
    {	civId: "wardens",	name: "Lady Eremiel's Fleet",	planetId: "japheth",	experience: 0,	ships: { salvador: 47525, ernst: 709e4, magrit: 1e7, miro: 1e7 },	},														
    {	civId: "wardens",	name: "Lady Rubiel's Fleet",	planetId: "poligon",	experience: 0,	ships: { salvador: 38815, ernst: 1e7, magrit: 709e4, miro: 1e7 },	},														
    {	civId: "wardens",	name: "Lady Jequn's Fleet",	planetId: "jardin",	experience: 0,	ships: { salvador: 2601662, ernst: 1e7, magrit: 1e7, miro: 1e7 },	},														
    {	civId: "wardens",	name: "Lady Ishim's Fleet",	planetId: "elon",	experience: 0,	ships: { salvador: 33354, ernst: 709e4, magrit: 1e7, miro: 95e5 },	},														
    {	civId: "wardens",	name: "Lady Miah's Fleet",	planetId: "yllirium",	experience: 0,	ships: { salvador: 29685, ernst: 1e7, magrit: 709e4, miro: 1e7 },	},														
    {	civId: "wardens",	name: "Lady Rassa's Fleet",	planetId: "mihandria",	experience: 0,	ships: { magrit: 47, miro: 5514 },	},														
    {	civId: "wardens",	name: "Lady Firiel's Fleet",	planetId: "misfir",	experience: 0,	ships: { ernst: 290, magrit: 291600, miro: 953e4 },	},														
    {	civId: "wardens",	name: "Lady Kushah's Fleet",	planetId: "hordron",	experience: 0,	ships: { salvador: 144, ernst: 341e3, magrit: 1728, miro: 3e6 },	},														
    {	civId: "wardens",	name: "Lady Bamiel's Fleet",	planetId: "parai",	experience: 0,	ships: { lifeSpark: 1 },	},														
    
    {	civId: "protokopta",	name: "Formicus Fleet",	planetId: "xirandrus",	experience: 0,	ships: { silverCarapax: 6, silverCarrier: 12, silverServant: 78 },	},														
    {	civId: "protokopta",	name: "Termitin Fleet",	planetId: "atlas",	experience: 0,	ships: { argentumSpina: 28, silverCarapax: 144, silverCarrier: 1728, silverServant: 20736 },	},														
    {	civId: "protokopta",	name: "Vesparion Fleet",	planetId: "mallus",	experience: 0,	ships: { silverCarapax: 1, silverCarrier: 20, silverServant: 28 },	},														
    {	civId: "protokopta",	name: "Kalabro Fleet",	planetId: "augmeris",	experience: 0,	ships: { argentumSpina: 5400, silverCarapax: 77760, silverCarrier: 1728, silverServant: 248832 },	},														
    {	civId: "protokopta",	name: "Koleopta Fleet",	planetId: "vanubis",	experience: 0,	ships: { argentumSpina: 936, silverCarapax: 27864, silverCarrier: 34992, silverServant: 248832 },	},														
]

var tutorialRefs = [
    {
        id: "tut0",
        text: "<div class='text-primary text-center h5 mb-4'>Welcome Commander</div>" +
              "<div class='text-normal text-center'>You finally woke up after a long cryosleep. 232 years have passed since you boarded the Vitha, but finally you reached your new home <span class='text-white'>Promision</span>.</div>" +
              "<div class='mt-2 text-danger text-center'>This version is a rewriting/remake of the original game. It is still under development so bugs and data lost could happen!</div>" +
              "<div class='mt-2 text-warning text-center'>You can disable this tutorial. To open it again, click on the icon <i class='fas fa-fw fa-question-circle'></i> in the bottom-right corner of the screen</div>",
        check: function(app) { return true },
        action: function(app) { if (app.currentPage != "planet" || app.currentPlanet.ref.id != "promision") app.showPlanetPage(app.game.planets['promision']) },
    },
    {
        id: "tut1",
        text: "<div class='text-primary text-center h5 mb-4'>Let's do a little briefing</div>" +
              "<div class='text-normal text-center'>In this page you can see basic infos about your planet.</div>" +
              "<div class='mt-2 text-normal text-center'>On the left you can see a list of resources that can be <span class='text-white'>extracted</span> on this planet, like <span class='text-white'>Iron</span>.</div>" +
              "<div class='mt-2 text-normal text-center'>Click on the icon <i class='fas fa-fw fa-hard-hat'></i> in the bottom menu to access the <span class='text-white'>Extraction</span> page.</div>",
        check: function(app) { return true },
        action: function(app) { if (app.currentPage != "planet" || app.currentPlanet.ref.id != "promision") app.showPlanetPage(app.game.planets['promision'] ) },
    },
    {
        id: "tut2",
        text: "<div class='text-primary text-center h5 mb-4'>Let's extract Iron</div>" +
              "<div class='text-normal text-center'>In this page, you can construct buildings to extract resources. By clicking on the desired building, you can see more details about it.</div>" +
              "<div class='mt-2 text-normal text-center'>On the left you can see how many resources are being produced every second.</div>" +
              "<div class='mt-2 text-normal text-center'>Now click on the icon <i class='fas fa-fw fa-plus-circle'></i>1 on the right of <span class='text-white'>Mining Plant</span> to build 1 more.</div>",
        check: function(app) { return app.currentPage == "extraction" && app.currentPlanet.ref.id == "promision" },
        action: function(app) { },
    },
    {
        id: "tut3",
        text: "<div class='text-primary text-center h5 mb-4'>Let's extract Iron</div>" +
              "<div class='text-normal text-center'>Perfect! You can now see on the right how <span class='text-white'>Iron</span> production has doubled!</div>" +
              "<div class='mt-2 text-normal text-center'>Keep building <span class='text-white'>Mining Plants</span>, until you reach 10 of them. Should only take few seconds!</div>",
        check: function(app) { return app.currentPlanet.buildings["mine"].count > 1 },
        action: function(app) { if (app.currentPage != "extraction" || app.currentPlanet.ref.id != "promision") { app.currentPlanet = app.game.planets["promision"]; app.showExtractionPage(); } },
    },
    {
        id: "tut4",
        text: "<div class='text-primary text-center h5 mb-4'>Let's go further</div>" +
              "<div class='text-normal text-center'>Perfect! But <span class='text-white'>Iron</span> is not the only resource you will need.</div>" +
              "<div class='mt-2 text-normal text-center'>Let's build a <span class='text-white'>Methane Extractor</span> to extract <span class='text-white'>Methane</span>.</div>",
        check: function(app) { return app.currentPlanet.buildings["mine"].count > 9 },
        action: function(app) { if (app.currentPage != "extraction" || app.currentPlanet.ref.id != "promision") { app.currentPlanet = app.game.planets["promision"]; app.showExtractionPage(); } },
    },
    {
        id: "tut5",
        text: "<div class='text-primary text-center h5 mb-4'>Let's go further</div>" +
              "<div class='text-normal text-center'>Great! But <span class='text-white'>Methane</span> alone is not that useful, we need <span class='text-white'>Fuel</span>.</div>" +
              "<div class='mt-2 text-normal text-center'>Click on the icon <i class='fas fa-fw fa-industry'></i> in the bottom menu to access the <span class='text-white'>Production</span> page.</div>",
        check: function(app) { return app.currentPlanet.buildings["methaneExtractor"].count > 0 },
        action: function(app) { if (app.currentPage != "extraction" || app.currentPlanet.ref.id != "promision") { app.currentPlanet = app.game.planets["promision"]; app.showExtractionPage(); } },
    },    
    {
        id: "tut6",
        text: "<div class='text-primary text-center h5 mb-4'>Tutorial in progress</div><div class='text-normal text-center'>Next steps of tutorial are under development.</div><div class='mt-2 text-normal text-center'>To be informed when new steps and new features will be ready, please join Discord server.</div><div class='mt-2 text-normal text-center'><a href='https://discord.gg/3UkgeeT9CV' target='_blank' class='btn text-white'>Join Discord server</a></div>",
        check: function(app) { return true },
        action: function(app) { },
    },
]

//------------------------------------------------------------------------------

class Tech {

    constructor(game, ref) {
    
        this.game = game
        this.ref = ref
        
        this.level = 0
    }
    
    //---
    
    getDescription() { return this.ref.description(this.level) }
    
    isVisible() {
        
        if (this.ref.planetReq) {
            
            if (this.game.planets[this.ref.planetReq].ref.civId == "human") return true
            else return false
        }
        
        return true
    }
    
    isUnlocked() {
    
        if (this.ref.techReqs) return this.game.areTechReqsSatisfied(this.ref.techReqs)
        else return true
    }
    
    getCostRP() { return this.ref.costRP * Math.pow(this.ref.multRP, this.level) }
    
    canLevelUpRP() { return this.isUnlocked() == true && this.game.researchPoint.count >= this.getCostRP() }

    //---
    
    levelUpRP() {
    
        if (this.canLevelUpRP() == true) {
            
            this.game.researchPoint.count -= this.getCostRP()
            this.level += 1
            
            this.ref.onLevelUp(this.level, this.game)
        }
    }
}

class Nebula {

    constructor(game, ref) {
    
        this.game = game
        this.ref = ref
        
        this.planets = {}
        
        this.routes = []
    }
    
    //---
    
    getVisiblePlanets() {
    
        let ret = {}
        
        for (let pId in this.planets) {
            let planet = this.planets[pId]
            
            if (this.game.planets['promision'].paths[pId].hops <= this.game.techs["astronomy"].level) {
                ret[pId] = planet
            }
        }
        
        return ret
    }

    getVisibleRoutes() {
    
        let ret = []
        
        this.routes.forEach(route => {
            if (route.planet1.isVisible() == true && route.planet2.isVisible()) {
                ret.push(route)
            }
        })
        
        return ret
    }
}

class Planet {

    constructor(game, ref) {
    
        this.game = game
        this.ref = ref
        
        this.nebula = null
        
        this.energy = { prod: 0, consum: 0, }
        this.researchPoint = { prod: 0, }
        
        this.ships = {}
        shipRefs.forEach(ref => {
            if (ref.civIds.includes("human") == true) {
                this.ships[ref.id] = new PlanetShip(this, ref)
            }
        })
        
        this.resources = {}        
        resourceRefs.forEach(ref => {
            this.resources[ref.id] = new PlanetResource(this, ref)
        })
        
        this.buildings = {}
        buildingRefs.forEach(ref => {
            if (ref.envs.includes(this.ref.env) == true) {
            
                if (ref.type == "extraction") {
                
                    let tmp = false
                    for (let rId in ref.prod) {
                        if (this.ref.prod[rId] != null) {
                            tmp = true
                            break
                        }
                    }
                    
                    if (tmp == true) {
                        this.buildings[ref.id] = new PlanetBuilding(this, ref)
                    }
                }
                else {
                    this.buildings[ref.id] = new PlanetBuilding(this, ref)
                }
            }
        })
        
        this.paths = {}
        
        this.fleets = []
        
        this.unlockTechId = null
    }
    
    //---
    
    isVisible() {
    
        let ret = false
        
        if (this.game.planets['promision'].paths[this.ref.id].hops <= this.game.techs["astronomy"].level) {
           ret = true
        }
        
        return ret
    }
    
    getUnlockedBuildingsByType(type) {
    
        let ret = {}
        
        for (let bId in this.buildings) {
            let building = this.buildings[bId]
            
            if (building.ref.type == type && building.isUnlocked() == true)
                ret[bId] = building
        }
        
        return ret
    }
    
    getEnergyCoeff() {
    
        let ret = 1
        
        if (this.energy.consum != 0) ret = Math.abs(this.energy.prod / this.energy.consum)
        if (ret > 1) ret = 1
        if (ret < 0) ret = 0
        
        return ret
    }
    
    canBuy(cost) {
    
        let ret = true
        
        for (let rId in cost) {
            if (cost[rId] >= 1 && this.resources[rId].count < cost[rId]) {            
                ret = false
                break
            }
        }
        
        return ret
    }

    getGroundShipCount() {
    
        let ret = 0

        for (let sId in this.ships) {
            let ship = this.ships[sId]
            ret += ship.count
        }
        
        return ret
    }
    
    getVisibleShips() {
    
        let ret = {}
        
        let arr = {}
        for (let sId in this.ships) {
            let ship = this.ships[sId]
            
            if (ship.ref.techReqs != null) {
                if (this.game.areTechReqsSatisfied(ship.ref.techReqs) == true) {
                    arr[sId] = ship
                }
            }
            else {
                arr[sId] = ship
            }
        }
        
        for (let sId in arr) {
            let ship = arr[sId]
            
            if (this.buildings["shipyard"].count >= (ship.ref.shipyardLevel - 2)) {
                ret[sId] = ship
            }
        }
        
        return ret
    }
    
    getUnlockedResources() {
    
        let ret = {}
        
        for (let rId in this.resources) {
            let resource = this.resources[rId]
            
            if (resource.isUnlocked() == true)
                ret[rId] = resource
        }
        
        return ret
    }
    
    getEnemyFleets() { return this.fleets.filter(fleet => fleet.civId != "human") }
    
    getAvailableRessources() {
    
        let ret = []
        
        for (let rId in this.resources) {
            if (this.resources[rId].count > 0) {
                ret.push(this.resources[rId])
            }
        }
        
        return ret
    }
    
    //---
    
    createFleet() {
        
        let shipsArr = {}
        for (let sId in this.ships) {
            let ship = this.ships[sId]
            
            if (ship.count > 0) {
            
                shipsArr[sId] = ship.count
                ship.count = 0
            }
        }
        
        this.fleets.push(new Fleet(this.game, "human", "New Fleet", this, 0, shipsArr, null))
    }
}

class PlanetResource {

    constructor(planet, ref) {
        
        this.planet = planet
        this.ref = ref
        
        this.prod = 0
        this.count = 0
    }
    
    //---
    
    isUnlocked() {
        
        let ret = true
        
        if (this.ref.techReqs && this.planet.game.areTechReqsSatisfied(this.ref.techReqs) == false) {
            ret = false
        }
        
        return ret
    }
}

class PlanetBuilding {

    constructor(planet, ref) {
        
        this.planet = planet
        this.ref = ref
        
        this.need = {}
        for (let rId in this.ref.prod) {
            if (this.ref.prod[rId] < 0) {
                this.need[rId] = false
            }
        }
        
        this.count = 0
        this.queue = 0
        this.active = true
        this.stoppedDelay = 0
    }
    
    //---
    
    isUnlocked() {
        
        if (this.ref.id == "spaceGateGamma") {
            return false
        }
        
        if (this.ref.id == "spaceGateDelta") {
            return false
        }
        
        let ret = true
        
        if (this.ref.techReqs && this.planet.game.areTechReqsSatisfied(this.ref.techReqs) == false) {
            ret = false
        }
        
        return ret
    }
    
    hasNeed() {
    
        for (let rId in this.need) {
            if (this.need[rId] == true) {
                return true
            }
        }
        
        return false
    }    

    getProd(count) {
    
        let ret = {}
        
        if (this.ref.energy > 0) {
            ret["energy"] = this.ref.energy * count
        }
        
        if (this.ref.researchPoint > 0) {
            ret["researchPoint"] = this.ref.researchPoint * count
        }
        
        for (let rId in this.ref.prod) {

            if (this.ref.type == "extraction") {
                if (this.ref.prod[rId] > 0 && this.planet.ref.prod[rId] > 0) {
                
                    ret[rId] = this.ref.prod[rId] * count
                    ret[rId] *= this.planet.ref.prod[rId]
                }
            }
            else {
            
                ret[rId] = this.ref.prod[rId] * count
                if (this.ref.prod[rId] > 0 && this.planet.ref.prod[rId]) {
                    ret[rId] *= this.planet.ref.prod[rId]
                }
            }
        }
        
        if (this.ref.energy < 0) {
            ret["energy"] = this.ref.energy * count
        }
        
        return ret
    }
    
    getCost(count) {
    
        let ret = {}
        
        for (let rId in this.ref.cost) {
        
            let t1 = Math.pow(this.ref.mult[rId], this.count)
            ret[rId] = this.ref.cost[rId] * t1
            
            for (let i = 1; i < count; i++) {
            
                t1 *= this.ref.mult[rId]
                ret[rId] += this.ref.cost[rId] * t1
            }
        }
        
        return ret
    }
    
    getRefund(count) {
    
        let ret = {}
        
        for (let rId in this.ref.cost) {
            ret[rId] = 0
            
            for (let n = 0; n < count; n++) {
                ret[rId] += (this.ref.cost[rId] * Math.pow(this.ref.mult[rId], this.count - n - 1))
            }
        }
        
        for (let rId in ret) {
            ret[rId] /= 2
        }
        
        return ret
    }

    canProduce() {
    
        let ret = true
        
        if (this.ref.type != "extraction") {
            for (let rId in this.ref.prod) {
                if (this.planet.resources[rId].count + (this.count * this.ref.prod[rId]) < 0 && this.planet.resources[rId].prod + (this.count * this.ref.prod[rId]) < 0) {
                    
                    ret = false
                    this.need[rId] = true
                    this.stoppedDelay = 1
                }
                else {
                
                    this.need[rId] = false
                }
            }
        }
        
        return ret
    }

    canBuild(count) {
    
        let cost = this.getCost(count)
        return this.planet.canBuy(cost)
    }
    
    //---
        
    toggleActive() { this.active = !this.active }
    
    emptyQueue() { this.queue = 0 }
    
    addQueue(count) { this.queue += count }
    
    destroy(count) {
    
        this.count -= count
        
        for (let rId in this.ref.prod) {
            this.need[rId] = false
        }
        
        let refund = this.getRefund(count)
        for (let rId in refund) {
            this.planet.resources[rId].count += refund[rId]
        }
    }
}

class PlanetShip {

    constructor(planet, ref) {
        
        this.planet = planet            
        this.ref = ref
        
        this.count = 0
    }
    
    //---
    
    isUnlocked() {
    
        let ret = true
        
        if (this.planet.buildings["shipyard"].count < this.ref.shipyardLevel) {
            ret = false
        }
        
        return ret
    }
    
    getCost(count) {
    
        let ret = {}
        
        for (let rId in this.ref.cost) {        
            ret[rId] = this.ref.cost[rId] * count
        }
        
        return ret
    }
    
    //---
        
    build(count) {
    
        for (let rId in this.ref.cost) {
            this.planet.resources[rId].count -= this.ref.cost[rId] * count        
        }
        
        this.count += count
    }
}

class Route {

    constructor(ref) {    
        
        this.ref = ref
        
        this.planet1 = null
        this.planet2 = null
    }
    
    //---
    
    cx() { return this.planet1.ref.x - this.planet2.ref.x }
    cy() { return this.planet1.ref.y - this.planet2.ref.y }
    
    distance() { return Math.floor(Math.sqrt(Math.pow(this.cx(), 2) + Math.pow(this.cy(), 2))) }

    getOtherPlanetId(planetId) {
        
        if (this.planet1.ref.id == planetId) return this.planet2.ref.id
        return this.planet1.ref.id
    }
}

class Fleet {

    constructor(game, civId, name, planet, experience, shipsArr, resourcesArr) {
                
        this.civId = civId
        this.game = game
        this.name = name
        this.planet = planet
        this.experience = experience
        
        this.resources = {}
        resourceRefs.forEach(ref => { this.resources[ref.id] = 0 })
        for (let rId in resourcesArr) {
            this.resources[rId] = resourcesArr[rId]
        }
        
        this.ships = {}
        shipRefs.forEach(ref => { this.ships[ref.id] = 0 })
        for (let rId in shipsArr) {
            this.ships[rId] = shipsArr[rId]
        }
    }
    
    //---
    
    getShipCount() {
    
        let ret = 0
        
        for (let sId in this.ships) {
            ret += this.ships[sId]            
        }
        
        return ret
    }
    
    getEngineBonus() {
    
        let ret = 1
        
        ret += this.resources["engine"] / 5e6
        
        return ret
    }
    
    getSpeed() {
    
        let ret = 1e7
        
        for (let sId in this.ships) {
            let ship = this.ships[sId]
            let ref = shipRefs.find(ref => ref.id == sId)
            
            if (ship > 0 && ref.speed < ret) {
                ret = ref.speed
            }
        }
        
        ret *= this.getEngineBonus()
        ret = Math.min(ret, 100)
        
        if (this.getShipCount() == 0) ret = 0
        
        return ret
    }
    
    getShips() {
    
        let ret = {}
        
        for (let sId in this.ships) {
            if (this.ships[sId] > 0) {
                ret[sId] = this.ships[sId]
            }
        }
        
        return ret
    }
    
    getMilitaryValue() {
    
        let raw = this.getRawMilitaryValue()
        let ammo = this.getAmmoBonus()
        
        let alkantara = 1 + .1 * Math.log(1 + this.ships["alkantara"]) / Math.log(2)
        
        let ret = 100 * (Math.log(1 + raw / 1) + Math.log(ammo / 1) + Math.log(alkantara / 1)) / Math.log(1.1)
        return ret == 0 ? 1 : ret
    }
    
    getRawMilitaryValue() {
        
        let ret = 0
        
        for (let sId in this.ships) {
            if (this.ships[sId] > 0) {
                
                let count = this.ships[sId]
                
                let ref = getShipRef(sId)
                let armorReduction = 1 - 1 / (1 + Math.log(1 + ref.armor * (1 + this.resources["armor"] / 2e6) / 1e4) / Math.log(2))
                
                let expBonus = 1 + 5e-4 * this.experience
                
                let temp = this.resources["engine"] / 5e6
                temp = 1 / (ref.speed * (1 + temp)) * 4.6 / Math.log(1500) - 2
                temp = .5 * (1.1 - 2 * temp / (1 + Math.abs(2 * temp)) * .9)
                ret += temp * count * ref.power * expBonus * count * ref.hp * expBonus / (1.001 - armorReduction) * ref.militaryValue
            }
        }
        
        return ret
    }
    
    getAmmoBonus() {
    
        let ret = 1
        
        ret += 10 * Math.log(1 + this.resources["ammunition"] / 1e7) / Math.log(2)
        ret += 20 * Math.log(1 + this.resources["uammunition"] / 1e7) / Math.log(2)
        ret += 60 * Math.log(1 + this.resources["tammunition"] / 2e7) / Math.log(2)
        
        return ret
    }
    
    getPower() {
        
        let ret = 0
        
        for (let sId in this.ships) {
            if (this.ships[sId] > 0) {
                
                let ref = getShipRef(sId)
                let count = this.ships[sId]
                
                let temp = ref.power * Math.ceil(count)
                ret += temp
            }
        }
        
        let expBonus = 1 + 5e-4 * this.experience
        let alkantara = 1 + .1 * Math.log(1 + this.ships["alkantara"]) / Math.log(2)
        
        ret *= this.getAmmoBonus() * expBonus * alkantara
        
        return ret
    }
    
    getHP() {
        
        let ret = 0
        
        for (let sId in this.ships) {
            if (this.ships[sId] > 0) {
                
                let ref = getShipRef(sId)
                let count = this.ships[sId]
                
                let temp = ref.hp * Math.ceil(count)
                ret += temp
            }
        }
        
        let expBonus = 1 + 5e-4 * this.experience
        
        ret *= expBonus
        
        return ret
    }
    
    getCurrentStorage() {
    
        let ret = 0
        
        for (let sId in this.resources) {
            ret += parseInt(this.resources[sId])
        }
        
        return ret
    }
    
    getTotalStorage() {
    
        let ret = 0
        
        for (let sId in this.ships) {
            if (this.ships[sId] > 0) {
                
                let ref = getShipRef(sId)
                let count = this.ships[sId]
                
                let temp = ref.storage * Math.ceil(count)
                ret += temp
            }
        }
        
        return ret
    }
    
    getAvailableStorage() {
    
        return this.getTotalStorage() - this.getCurrentStorage()
    }
    
    getAvailableResources() {
    
        let ret = []
        
        for (let rId in this.resources) {
            if (this.resources[rId] > 0) {
                ret.push({ id:rId, count:this.resources[rId] })
            }
        }
        
        return ret
    }
        
    //---
        
    move(originId, destinationId) {
        
        let index = this.planet.fleets.indexOf(this)
        this.planet.fleets.splice(index, 1)
        
        this.planet = null
        
        this.game.travels.push(new Travel(this.game, this, originId, destinationId))
    }

    rename(name) {
    
        this.name = name
    }

    colonize() {
    
        this.planet.ref.civId = "human"
        
        this.ships["vitha"] -= 1
        
        if (this.getShipCount() <= 0) {
            
            let index = this.planet.fleets.indexOf(this)
            this.planet.fleets.splice(index, 1)
            
            delete this
        }
        
        let ref = getShipRef("vitha")
        for (let rId in ref.cost) {
        
            this.planet.resources[rId].count += .5 * ref.cost[rId]
        }
    }

    battle(enemyFleet) {
    
        let report = new BattleReport(this, enemyFleet)
        
        let toContinue = true
        while (toContinue == true) {
        
            report.newTurn()
        
            let fleet1Damages = report.fleet1.getDamages()
            let fleet2Damages = report.fleet2.getDamages()
            
            let fleet1Results = report.fleet1.processDamages(fleet2Damages)
            let fleet2Results = report.fleet2.processDamages(fleet1Damages)
            
            report.fleet1.updateWithResults(fleet1Results)
            report.fleet2.updateWithResults(fleet2Results)
            
            if (report.fleet1.getShipCount() > 0 && report.fleet2.getShipCount() > 0) toContinue = true
            else toContinue = false
        }
        
        if (report.fleet1.getDamages() > 0) {
            report.winning = true
        }
        
        for (let sId in this.ships) {
        
            this.ships[sId] = report.fleet1.ships[sId].remainingCount
            enemyFleet.ships[sId] = report.fleet2.ships[sId].remainingCount
        }
        
        if (this.getShipCount() <= 0) {
        
            let index = this.planet.fleets.indexOf(this)
            this.planet.fleets.splice(index, 1)
            
            delete this
        }
        
        if (enemyFleet.getShipCount() <= 0) {
        
            let index = enemyFleet.planet.fleets.indexOf(enemyFleet)
            enemyFleet.planet.fleets.splice(index, 1)
            
            delete this.game.planets[enemyFleet.planet.ref.id].fleets[index]
        }
        
        return report
    }

    load(resourcesArr, planet) {
        
        if (planet == null) { planet = this.planet }
        
        for (let rId in resourcesArr) {
        
            this.resources[rId] += parseInt(resourcesArr[rId])
            planet.resources[rId].count -= parseInt(resourcesArr[rId])
        }
    }
    
    unload(resourcesArr, planet) {
        
        if (planet == null) { planet = this.planet }
        
        for (let rId in resourcesArr) {
        
            this.resources[rId] -= parseInt(resourcesArr[rId])
            planet.resources[rId].count += parseInt(resourcesArr[rId])
        }
    }

    autoroute(originId, destinationId, originPercentages, destinationPercentages) {

        this.unload(this.resources)
        
        let totalStorage = this.getTotalStorage()
        
        let loadResources = {}
        for (let rId in originPercentages) {        
            loadResources[rId] = Math.min(originPercentages[rId] / 100 * totalStorage, this.planet.resources[rId].count)
        }
        this.load(loadResources)
        
        let index = this.planet.fleets.indexOf(this)
        this.planet.fleets.splice(index, 1)
        
        this.planet = null

        this.game.travels.push(new Autoroute(this.game, this, originId, destinationId, originPercentages, destinationPercentages))
    }
}

class Travel {

    constructor(game, fleet, originId, destinationId, remainingSeconds) {
    
        this.game = game
        this.type = "normal"
        this.fleet = fleet
        this.origin = this.game.planets[originId]
        this.destination = this.game.planets[destinationId]
        
        if (remainingSeconds) {
        
            this.remainingSeconds = remainingSeconds
        }
        else {
        
            this.remainingSeconds = this.origin.paths[destinationId].distance / this.fleet.getSpeed()
        }
    }
    
    onArrive() {
    
        let index = this.game.travels.indexOf(this)
        this.game.travels.splice(index, 1)
        
        this.fleet.planet = this.destination
        this.destination.fleets.push(this.fleet)
    }
}

class Autoroute extends Travel {

    constructor(game, fleet, originId, destinationId, originPercentages, destinationPercentages, lastPlanetId, remainingSeconds) {
        super(game, fleet, originId, destinationId, remainingSeconds)
        
        this.type = "autoroute"
        
        this.originPercentages = originPercentages
        this.destinationPercentages = destinationPercentages
        
        if (lastPlanetId) {
        
            this.lastPlanet = this.game.planets[lastPlanetId]
        }
        else {
        
            this.lastPlanet = this.origin
        }
    }
    
    onArrive() {
        
        let loadPercentages
        
        if (this.lastPlanet == this.origin) {
            
            loadPercentages = this.destinationPercentages            
            this.lastPlanet = this.destination
        }
        else {
        
            loadPercentages = this.originPercentages            
            this.lastPlanet = this.origin
        }
        
        this.fleet.unload(this.fleet.resources, this.lastPlanet)
        
        let totalStorage = this.fleet.getTotalStorage()
        
        let loadResources = {}
        for (let rId in loadPercentages) {        
            loadResources[rId] = Math.min(loadPercentages[rId] / 100 * totalStorage, this.lastPlanet.resources[rId].count)
        }
        this.fleet.load(loadResources, this.lastPlanet)
        
        this.remainingSeconds = this.origin.paths[this.destination.ref.id].distance / this.fleet.getSpeed()
    }
}

class BattleReport {

    constructor(attackerFleet, defenderFleet) {
    
        this.fleet1 = new BattleFleet(this, attackerFleet)
        this.fleet2 = new BattleFleet(this, defenderFleet)
        
        this.turns = []
        
        this.currentTurn = null
        
        this.winning = false
    }
    
    //---
        
    toHtml(app) {
    
        let ret = ""
        ret += "<div class='text-primary text-center h5 mb-4'>Battle Report</div>"
        
        this.turns.forEach(turn => {
        
            ret += "<div class='text-gray text-start h6 mb-2'>Turn " + turn.id + "</div>"
            ret += "<div class='pb-2 mb-3 border-bottom'>"
            
            let lastFleet = this.fleet1
            turn.damages.forEach(damage => {
                
                if (damage.fleet != lastFleet) {
                
                    lastFleet = damage.fleet
                    ret += "<div class='my-2'></div>"
                }
                
                ret += "<div class='row'>"
                ret += "<div class='col text-start text-white'>" + app.$t('shipName_' + damage.ship) + "</div>"
                // ret += "<div class='col-auto text-normal'>" + damage.damage + "</div>"
                // ret += "<div class='col-auto text-normal'>" + damage.quotient + "</div>"
                ret += "<div class='col-auto text-start text-white' style='width:75px;'><i class='small text-success fa fa-fw fa-rocket'></i> " + Math.round(damage.remaining) + "</div>"
                ret += "<div class='col-auto text-start text-white' style='width:75px;'><i class='small text-danger fa fa-fw fa-skull'></i> " + Math.round(damage.killed) + "</div>"
                ret += "</div>"
            })
            
            ret += "</div>"
        })
        
        if (this.winning == true) ret += "<div class='text-success text-center h5 mt-4 border border-success rounded'>Victory</div>"
        else ret += "<div class='text-primary text-danger h5 mt-4 border border-danger rounded'>Defeat</div>"
        
        return ret
    }
    
    //---
        
    newTurn() {
        
        let turn = { id:this.turns.length + 1, damages:[], results:[], }
        
        this.turns.push(turn)
        
        this.currentTurn = turn
    }
}

class BattleFleet {

    constructor(report, fleet) {
        
        this.report = report
        this.fleet = fleet
        
        this.ships = {}
        shipRefs.forEach(ref => { this.ships[ref.id] = {
        
            initialCount:fleet.ships[ref.id],
            remainingCount:fleet.ships[ref.id],
        } })
    }
    
    //---
        
    getDamages() {
        
        let ret = 0
        let ret2 = 0
        
        for (let sId in this.ships) {
            if (this.ships[sId].remainingCount > 0) {
                
                let ref = getShipRef(sId)
                let count = this.ships[sId].remainingCount
                
                let temp = ref.power * Math.ceil(count)
                ret += temp
                
                temp = ref.piercing * Math.ceil(count)
                ret2 += temp
            }
        }
        
        let expBonus = 1 + 5e-4 * this.fleet.experience
        let alkantara = 1 + .1 * Math.log(1 + this.ships["alkantara"].remainingCount) / Math.log(2)
        
        ret *= this.fleet.getAmmoBonus() * expBonus * alkantara
        
        return { damages:ret, piercing:ret2 }
    }
    
    getShipCount() {
    
        let ret = 0
        
        for (let sId in this.ships) {
            ret += this.ships[sId].remainingCount
        }
        
        return ret
    }
    
    getShipArmor(sId) {
    
        let ret = getShipRef(sId).armor
        
        let expBonus = 1 + 5e-4 * this.fleet.experience
        let armorBonus = 1 + this.fleet.resources["armor"] / 1e3 * 5e-4
        
        ret *= expBonus * armorBonus
        
        return ret
    }
    
    getShipShield(sId) {
    
        let expBonus = 1 + 5e-4 * this.fleet.experience

        return (getShipRef(sId).shield + 1e3) * (1 + .1 / Math.log(2)) * expBonus
    }
    
    //---
        
    processDamages(damages) {
        
        let results = {}
        
        let totalWeight = 0
        for (let sId in this.ships) {
            if (this.ships[sId].remainingCount > 0) {
                
                let ref = getShipRef(sId)
                let count = this.ships[sId].remainingCount
                
                totalWeight += ref.weight * Math.ceil(count)
            }
        }
        
        for (let sId in this.ships) {
            if (this.ships[sId].remainingCount > 0) {
            
                let ref = getShipRef(sId)
                let count = this.ships[sId].remainingCount
                
                let quotient = (ref.weight * Math.ceil(count)) / totalWeight
                let damage = damages.damages * quotient
                                
                let shieldProtection = this.getShipShield(sId)
                damage -= shieldProtection * count
                if (damage < 0) damage = 0
                
                let piercing = damages.piercing * quotient
                let piercingCoeff = 1 - piercing / (this.getShipArmor(sId) * count)
                if (piercingCoeff < 0) piercingCoeff = 0
                
                let armorReduction = 1 - 1 / (1 + Math.log(1 + (this.getShipArmor(sId) * piercingCoeff) / 1e4) / Math.log(2))
                damage *= armorReduction
                
                let killed = damage / ref.hp
                killed = Math.min (killed, count)
                
                if (results[sId]) results[sId] += killed
                else results[sId] = killed
                
                this.report.currentTurn.damages.push({ fleet:this, ship:sId, count:count, damage:damage, quotient:quotient, remaining:count - killed, killed:killed, })
            }
        }
        
        return results
    }
    
    updateWithResults(results) {
        
        for (let sId in results) {
            this.ships[sId].remainingCount -= results[sId]
        }
    }
}

class Tutorial {

    constructor(game, ref) {
    
        this.game = game
        this.ref = ref
        
        this.done = false
    }
}

class Game {

    constructor() {
    
        this.paused = false        
        this.bonusActive = false
        this.tutorialActive = true
        this.resetInProgress = false
        
        this.days = 0
        this.timeTravelCount = 0
        
        this.bonus = { count:0 }
        this.researchPoint = { count:0, prod:0 }
        
        this.techs = {}
        this.planets = {}
        this.nebulas = {}
        this.tutorials = {}
        
        this.routes = []
        this.travels = []
    }
    
    //---
    
    getInfluence() {
        
        let ret = 0
        
        let planets = this.getHumanPlanets()
        for (let pId in planets) {
            ret += planets[pId].ref.influence
        }
        
        return ret
    }
    
    getHumanPlanets() {
    
        let ret = {}
        
        for (let pId in this.planets) {
            let planet = this.planets[pId]
            
            if (planet.ref.civId == "human") {
                ret[planet.ref.id] = planet
            }
        }
        
        return ret
    }
    
    areTechReqsSatisfied(techReqs) {
    
        for (let tId in techReqs) {
            if (this.techs[tId].level < techReqs[tId]) {
                return false
            }
        }
        
        return true
    }
    
    getOrbittingFleets() {
    
        let ret = []
        
        for (let pId in this.planets) {
            let planet = this.planets[pId]
            
            for (let fId in planet.fleets) {
                let fleet = planet.fleets[fId]
                
                if (fleet.civId == "human") {
                    ret.push(fleet)
                }
            }
        }
        
        return ret
    }
    
    //---
    
    buildData() {
        
        techRefs.forEach(ref => { this.techs[ref.id] = new Tech(this, ref) })
        nebulaRefs.forEach(ref => { this.nebulas[ref.id] = new Nebula(this, ref) })        
        planetRefs.forEach(ref => { this.planets[ref.id] = new Planet(this, ref) })
        tutorialRefs.forEach(ref => { this.tutorials[ref.id] = new Tutorial(this, ref) })
        
        for (let tId in this.techs) {
            let tech = this.techs[tId]
            
            if (tech.ref.planetReq != null) {
                this.planets[tech.ref.planetReq].unlockTechId = tId
            }
        }
        
        routeRefs.forEach(ref => {
            let route = new Route(ref)
            
            route.planet1 = this.planets[ref.pl1Id]
            route.planet2 = this.planets[ref.pl2Id]
            
            this.routes.push(route)
        })
        
        for (let nId in this.nebulas) {
            let nebula = this.nebulas[nId]                
            
            for (let pId in this.planets) {
                let planet = this.planets[pId]
                
                if (planet.ref.nebulaId == nebula.ref.id) nebula.planets[pId] = planet
            }
            
            this.routes.forEach(route => {
                if (route.planet1.ref.nebulaId == nId) nebula.routes.push(route)
            })
        }
        
        for (let pId in this.planets) {
            let planet = this.planets[pId]
            
            planet.paths[pId] = { distance:0, route:null, hops:0 }
            
            let routesArr = []
            this.routes.forEach(route => {
                if (route.ref.pl1Id == pId || route.ref.pl2Id == pId) routesArr.push(route)
            })
        
            routesArr.forEach(route => {
                planet.paths[route.getOtherPlanetId(pId)] = { distance:route.distance(), route:route, hops:0 }
            })

            routesArr.forEach(route => {
                this.buildPath(planet.paths, route.getOtherPlanetId(pId), route.distance(), route, 1)
            })
        }
    }
    
    buildPath(paths, pId, distance, route, hops) {
        
        let routesArr = []
        for (let rId in this.routes) {
            let rte = this.routes[rId]
            if (rte.ref.pl1Id == pId || rte.ref.pl2Id == pId) {
                routesArr.push(rte)
            }
        }
        
        let arr = []
        routesArr.forEach(rte => {
        
            let otherId = rte.getOtherPlanetId(pId)                
            if (paths[otherId]) {                
                if (paths[otherId].distance > distance + rte.distance()) {
                
                    paths[otherId].distance = distance + rte.distance()
                    paths[otherId].route = route
                    arr.push({ planetId:otherId, distance:distance + rte.distance() })
                }
            }
            else {
            
                paths[otherId] = { distance:distance + rte.distance(), route:route, hops:hops + 1 },
                arr.push({ planetId:otherId, distance:distance + rte.distance() })
            }
        })
        
        arr.forEach(item => {
            this.buildPath(paths, item.planetId, item.distance, route, hops + 1)
        })
    }
    
    initStartingData() {
        
        fleetRefs.forEach(ref => {
            
            console.log(ref.planetId)
            let planet = this.planets[ref.planetId]
            let fleet = new Fleet(this, ref.civId, ref.name, planet, ref.experience, ref.ships, null)
            planet.fleets.push(fleet)            
        })
        
        this.planets["promision"].buildings["mine"].count = 1
    }
    
    //---
    
    loadFromData(data) {
        
        this.days = data.days || 0
        this.bonus.count = data.bonusCount || 0
        this.timeTravelCount = data.timeTravelCount || 0
        this.tutorialActive = data.tutorialActive
        this.researchPoint.count = data.researchPointCount || 0
        
        for (let tId in this.techs) {            
            let tech = data.techs[tId]
            
            if (tech && tech.level > 0) {
            
                for (let i = 0; i < tech.level; i++) {
                
                    this.techs[tId].level += 1
                    this.techs[tId].ref.onLevelUp(tech.level, this)
                }
            }
        }
        
        for (let pId in this.planets) {
            let planet = data.planets[pId]
            if (planet) {
            
                let ref =  planetRefs.find(ref => ref.id == pId)
                ref.civId = planet.civId
                
                if (data.planets[pId].buildings) {
                    for (let bId in this.planets[pId].buildings) {
                    
                        let building = data.planets[pId].buildings[bId]
                        if (building) {
                        
                            this.planets[pId].buildings[bId].count = building.count
                            this.planets[pId].buildings[bId].queue = building.queue
                            this.planets[pId].buildings[bId].active = building.active
                        }
                    }
                }
                
                if (data.planets[pId].resources) {
                    for (let rId in this.planets[pId].resources) {
                    
                        let resource = data.planets[pId].resources[rId]
                        if (resource) {
                        
                            this.planets[pId].resources[rId].count = resource.count
                        }
                    }
                }
                
                if (data.planets[pId].ships) {
                    for (let sId in this.planets[pId].ships) {
                    
                        let ship = data.planets[pId].ships[sId]
                        if (ship) {
                        
                            this.planets[pId].ships[sId].count = ship.count
                        }
                    }
                }
                
                if (data.planets[pId].fleets) {
                    data.planets[pId].fleets.forEach(fleet => {
                        
                        this.planets[pId].fleets.push(new Fleet(this, fleet.civId, fleet.name, this.planets[pId], fleet.experience, fleet.ships, fleet.resources))
                    })
                }
            }
        }
        
        if (data.tutorials) {
            for (let tId in this.tutorials) {
                let tutorial = data.tutorials[tId]
                if (tutorial && tutorial.done == true) {            
                    this.tutorials[tId].done = tutorial.done
                }
            }
        }
        
        if (data.travels) {
            data.travels.forEach(travel => {
                
                let fleet = new Fleet(this, travel.fleet.civId, travel.fleet.name, null, travel.fleet.experience, travel.fleet.ships, travel.fleet.resources)
                
                if (travel.type == "autoroute") {
                
                    this.travels.push(new Autoroute(this, fleet, travel.originId, travel.destinationId, travel.originPercentages, travel.destinationPercentages, travel.lastPlanetId, travel.remainingSeconds))
                }
                else {
                
                    this.travels.push(new Travel(this, fleet, travel.originId, travel.destinationId, travel.remainingSeconds))
                }
            })
        }
    }
    
    getSavedData() {
    
        let ret = {
            
            days: this.days,
            bonusCount: this.bonus.count,
            researchPointCount: this.researchPoint.count,
            timeTravelCount: this.timeTravelCount,
            tutorialActive: this.tutorialActive,
            
            techs: {},
            planets: {},
            tutorials: {},
            
            travels: [],
        }
        
        for (let rId in this.techs) {
            let tech = this.techs[rId]
            
            ret.techs[rId] = { id: tech.ref.id, level: tech.level, }
        }
        
        for (let pId in this.planets) {
            let planet = this.planets[pId]
            
            ret.planets[pId] = { id: planet.ref.id, civId: planet.ref.civId, buildings: {}, resources: {}, ships: {},  fleets: [], }
            
            for (let rId in this.planets[pId].resources) {
                let resource = this.planets[pId].resources[rId]
                
                ret.planets[pId].resources[rId] = { id: resource.ref.id, count: resource.count }
            }
            
            for (let bId in this.planets[pId].buildings) {
                let building = this.planets[pId].buildings[bId]
                
                ret.planets[pId].buildings[bId] = { id: building.ref.id, count: building.count, queue: building.queue, active: building.active }
            }
        
            for (let sId in this.planets[pId].ships) {
                let ship = this.planets[pId].ships[sId]
                
                ret.planets[pId].ships[sId] = { id: ship.ref.id, count: ship.count, }
            }
            
            this.planets[pId].fleets.forEach(fleet => {
            
                let data = {}
                
                data.civId = fleet.civId
                data.name = fleet.name
                data.experience = fleet.experience        
                data.resources = fleet.resources
                data.ships = fleet.ships
                
                ret.planets[pId].fleets.push(data)
            })
        }

        for (let tId in this.tutorials) {
            let tutorial = this.tutorials[tId]
            
            if (tutorial.done == true) {
                ret.tutorials[tId] = { id: tutorial.ref.id, done: tutorial.done, }
            }
        }
        
        this.travels.forEach(travel => {
        
            let data = {}
            
            data.type = travel.type
            data.originId = travel.origin.ref.id
            data.destinationId = travel.destination.ref.id        
            data.remainingSeconds = travel.remainingSeconds            
            
            data.fleet = {
            
                civId: travel.fleet.civId,
                name: travel.fleet.name,
                experience: travel.fleet.experience,      
                resources: travel.fleet.resources,
                ships: travel.fleet.ships,
            }
            
            if (travel.type == "autoroute") {
            
                data.originPercentages = travel.originPercentages
                data.destinationPercentages = travel.destinationPercentages
                
                data.lastPlanetId = travel.lastPlanet.ref.id
            }
            
            ret.travels.push(data)
        })
        
        return ret
    }
    
    importLocaleData() {
    }
    
    wipeLocaleData() {
    }
    
    //---
    
    mainLoop(deltaTime) {
    
        if (this.paused == false) {
            
            let coeff = 1
            
            let delta = deltaTime / 1000
            if (this.bonusActive == true && this.bonus.count > 0) {
            
                this.bonus.count -= delta * 4000
                if (this.bonus.count < 0) {
                
                    this.bonus.count = 0
                    this.bonusActive = false
                }
                
                coeff = 5
            }
            
            this.days += .25 * delta * coeff
            
            this.produceResources(delta * coeff)
            this.buildBuildings()
            this.updateTravels(delta * coeff)
        }
    }
    
    produceResources(delta) {
        
        this.researchPoint.prod = 0
        
        for (let pId in this.planets) {
            let planet = this.planets[pId]
            
            if (planet.ref.civId == 'human') {       
                
                for (let rId in planet.resources) {
                    let resource = planet.resources[rId]
                    
                    resource.prod = 0
                }
                
                let energy = { prod: 0, consum: 0 }
                planet.researchPoint.prod = 0
                
                for (let bId in planet.buildings) {
                    let building = planet.buildings[bId]
                    
                    if (building.count > 0 && building.active) {
                        
                        if (building.stoppedDelay > 10) building.stoppedDelay = 0
                        else if (building.stoppedDelay > 0) building.stoppedDelay += delta
                        
                        if (building.stoppedDelay <= 0) {
                            
                            if (building.canProduce() == true) {
                            
                                let energyCoeff = planet.getEnergyCoeff()
                                if (building.ref.energy >= 0) energyCoeff = 1
                                
                                for (let rId in building.ref.prod) {
                                    
                                    if (building.ref.type == "extraction") {
                                        if (building.ref.prod[rId] > 0 && planet.ref.prod[rId] > 0) {
                                        
                                            planet.resources[rId].prod += building.ref.prod[rId] * building.count * energyCoeff
                                            planet.resources[rId].prod *= planet.ref.prod[rId]
                                        }
                                    }
                                    else {
                                    
                                        planet.resources[rId].prod += building.ref.prod[rId] * building.count * energyCoeff
                                        if (building.ref.prod[rId] > 0 && planet.ref.prod[rId] > 0) {
                                            planet.resources[rId].prod *= planet.ref.prod[rId]
                                        }
                                    }
                                }
                                
                                planet.researchPoint.prod += building.ref.researchPoint * building.count * energyCoeff
                                
                                if (building.ref.energy > 0) energy.prod += building.ref.energy * building.count
                                else if (building.ref.energy < 0) energy.consum += building.ref.energy * building.count
                            }
                       }
                    }
                }
                
                this.researchPoint.prod += planet.researchPoint.prod
                
                planet.energy = energy
                
                for (let rId in planet.resources) {
                    let resource = planet.resources[rId]
                    
                    resource.count += resource.prod * delta
                    if (resource.count < 0) resource.count = 0
                }
            }
        }
        
        this.researchPoint.count += this.researchPoint.prod * delta
    }
    
    buildBuildings() {
    
        for (let pId in this.planets) {
            let planet = this.planets[pId]
            
            if (planet.ref.civId == 'human') {
            
                for (let bId in planet.buildings) {
                    let building = planet.buildings[bId]
                    
                    for (let i = 0; i < building.queue; i++) {
                        let cost = building.getCost(1)
                        if (planet.canBuy(cost) == true) { 
                        
                            building.count += 1
                            building.queue -= 1
                            
                            for (let rId in cost)
                                if (cost[rId] >= 1)
                                    planet.resources[rId].count -= cost[rId]                                
                        }                            
                        else break
                    }
                }
            }
        }
    }

    updateTravels(delta) {
        
        for (let tId = 0; tId < this.travels.length; tId++) {
            let travel = this.travels[tId]
            if (travel.remainingSeconds - delta <= 0) {
                
                travel.onArrive()
            }
            else {
            
                travel.remainingSeconds -= delta
            }
        }
    }    
}

//------------------------------------------------------------------------------

import LZString from 'lz-string'

export default {
    
    data() {
        return {
            
            fps: 60,
            error: null,
            locale: 'en',
            started: false,
            isMobile: false,
            rafHandle: null,
            autoSaveDelay: 30000,
            lastFrameTimeMs: new Date().getTime(),
            localStorageName: 'fghog',
            importExportData: null,
            minLoadingTimerMS: 1000,
            
            //---

            toastText: null,
            toastType: 'info',
            toastTimeout: null,
            
            //---
            
            popupText: null,
            popupTextOk: "Ok",
            popupTextOkAction: null,
            popupTextCancel: "Cancel",
            popupTextCancelAction: null,
            
            //---

            popupMoveFleet: null,
            popupMoveDestination: null,
            //---

            popupAutorouteFleet: null,
            popupAutorouteDestination: null,
            popupAutorouteOriginPercentages: {},
            popupAutorouteDestinationPercentages: {},
            popupAutorouteTotalOrigin: 0,
            popupAutorouteTotalDestination: 0,
            
            //---
                        
            popupRenameFleet: null,
            popupRenameName: null,
            
            //---
                        
            popupAttackFleet: null,
            popupAttackEnemyFleet: null,
            
            //---
            
            popupLoadFleet: null,           
            popupLoadResources: {},
            popupLoadCurrentStorage: 0,
            
            //---
            
            popupUnloadFleet: null,           
            popupUnloadResources: {},
            
            //---
            
            currentPage: 'planet',
            currentFleetsMenu: 'ground',

            //---
            
            currentShip: null,
            currentPlanet: null,
            currentNebula: null,
            currentTravel: null,
            currentBuilding: null,
            currentResearch: null,
            currentFleetsGround: null,
            currentFleetsOrbitting: null,
            
            currentTravelIndex: -1,
            currentFleetsOrbittingIndex: -1,
            
            //---
            
            currentShipCount: 1,
            currentBuildCount: 1,
            currentDestroyCount: 1,            
            
            //---
            
            game: new Game(),
        }
    },
    
    computed: {
        
        days() {
        
            return parseInt(this.game.days) - 365 * this.years
        },
        
        years() {
        
            return parseInt(this.game.days / 365)
        },
        
        exportGameData() {
        
            let text = localStorage.getItem(this.localStorageName)
            return text
        },        
    },
    
    methods: {
        
        nebulaNext() {
        
            if (this.currentNebula.ref.id == 'perseus') this.currentNebula = this.game.nebulas['andromeda']
            else if (this.currentNebula.ref.id == 'andromeda') this.currentNebula = this.game.nebulas['void']
            else if (this.currentNebula.ref.id == 'void') this.currentNebula = this.game.nebulas['perseus']
        },
        
        nebulaPrevious() {
        
            if (this.currentNebula.ref.id == 'perseus') this.currentNebula = this.game.nebulas['void']
            else if (this.currentNebula.ref.id == 'andromeda') this.currentNebula = this.game.nebulas['perseus']
            else if (this.currentNebula.ref.id == 'void') this.currentNebula = this.game.nebulas['andromeda']
        },
        
        currentPlanetNext() {
            
            let keys = Object.keys(this.game.getHumanPlanets())
            let currentIndex = keys.indexOf(this.currentPlanet.ref.id)
            let nextIndex = (currentIndex + 1) % keys.length
            
            this.currentPlanet = this.game.planets[keys[nextIndex]]
            this.currentNebula = this.game.nebulas[this.currentPlanet.ref.nebulaId]
        },
        
        currentPlanetPrevious() {
            
            let keys = Object.keys(this.game.getHumanPlanets())
            let currentIndex = keys.indexOf(this.currentPlanet.ref.id)
            let nextIndex = (currentIndex - 1) % keys.length
            if (nextIndex < 0) nextIndex = keys.length -1
            
            this.currentPlanet = this.game.planets[keys[nextIndex]]
            this.currentNebula = this.game.nebulas[this.currentPlanet.ref.nebulaId]
        },
        
        //---
        
        showFleetsPage() {
            
            if (this.game.techs["astronomy"].level > 0) this.currentPage = "fleets"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        showMapPage() {
            
            if (this.game.techs["astronomy"].level > 0) this.currentPage = "map"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        showResearchPage() { this.currentPage = "research" },
        
        showOverviewPage() { this.currentPage = "overview" },
        
        showPlanetPage(planet) {
            
            this.currentPage = "planet"
            this.currentPlanet = planet
        },
        
        showExtractionPage() { this.currentPage = "extraction" },
        
        showProductionPage() { this.currentPage = "production" },
        
        showEnergyPage() { this.currentPage = "energy" },
        
        showLabsPage() { this.currentPage = "labs" },
        
        showOthersPage() { this.currentPage = "others" },
        
        showShipyardPage() {
            
            if (this.game.techs["astronomy"].level > 0) this.currentPage = "shipyard"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        showSettingsPage() { this.currentPage = "settings" },
        
        //---
        
        showTutorial() {
        
            this.game.tutorialActive = true
            
            this.processTutorial()            
        },
        
        showHardResetPopup() {
        
            this.popupText = "<div class='h5 text-danger mb-2'>Hard Reset</div><span class='text-danger'>Are you sure you want to wipe your data?<br>You will lose ALL your progress!</span>"
            this.popupTextOkAction = function() { this.resetGameData(); this.closePopup(); }
            this.popupTextCancelAction = this.closePopup
        },
        
        showAttackPopup(fleet) {
        
            this.popupAttackFleet = fleet
        },
        
        showPopupLoad(fleet) {
        
            this.popupLoadFleet = fleet
            
            this.popupLoadResources = {}        
            resourceRefs.forEach(ref => {
                this.popupLoadResources[ref.id] = 0
            })
            
            this.popupLoadCurrentStorage = 0
        },

        refreshPopupLoadCurrentStorage() {
        
            let ret = this.popupLoadFleet.getCurrentStorage()
            
            for (let rId in this.popupLoadResources) {
                ret += parseInt(this.popupLoadResources[rId])
            }
            
            this.popupLoadCurrentStorage = ret
        },

        showPopupUnload(fleet) {
        
            this.popupUnloadFleet = fleet
            
            this.popupUnloadResources = {}        
            resourceRefs.forEach(ref => {
                this.popupUnloadResources[ref.id] = 0
            })
        },

        refreshPopupUnloadCurrentStorage() {
        
            let ret = this.popupUnloadFleet.getCurrentStorage()
            
            for (let rId in this.popupUnloadResources) {
                ret -= parseInt(this.popupUnloadResources[rId])
            }
            
            this.popupUnloadCurrentStorage = ret
        },
        
        showPopupAutoroute(fleet) {
        
            this.popupAutorouteFleet = fleet
            this.popupAutorouteDestination = null
            this.popupAutorouteOriginPercentages = {}
            this.popupAutorouteDestinationPercentages = {}
            this.popupAutorouteTotalOrigin = 0
            this.popupAutorouteTotalDestination = 0
            
            resourceRefs.forEach(ref => {
                if (ref.techReqs == null || this.game.areTechReqsSatisfied(ref.techReqs) == true) {
                
                    this.popupAutorouteOriginPercentages[ref.id] = 0
                    this.popupAutorouteDestinationPercentages[ref.id] = 0
                }
            })
        },
        
        refreshPopupAutorouteTotalOrigin() {
            
            let totalStorage = this.popupAutorouteFleet.getTotalStorage()
            
            let ret = 0
            for (let rId in this.popupAutorouteOriginPercentages) {
                ret += parseInt(this.popupAutorouteOriginPercentages[rId] / 100 * totalStorage)
            }
            
            this.popupAutorouteTotalOrigin = ret
        },
        
        refreshPopupAutorouteTotalDestination() {
            
            let totalStorage = this.popupAutorouteFleet.getTotalStorage()
            
            let ret = 0
            for (let rId in this.popupAutorouteDestinationPercentages) {
                ret += parseInt(this.popupAutorouteDestinationPercentages[rId] / 100 * totalStorage)
            }
            
            this.popupAutorouteTotalDestination = ret
        },
        
        closePopup() {
        
            this.popupText = null
            this.popupTextOk = "Ok"            
            this.popupTextOkAction = null
            this.popupTextCancel = "Cancel"
            this.popupTextCancelAction = null
        },
       
        //---
        
        setCurrentShip(ship) { this.currentShip = ship },
        
        setCurrentBuilding(building) { this.currentBuilding = building },
        
        setCurrentResearch(research) { this.currentResearch = research },
        
        setCurrentTravel(index, travel) {
            
            this.currentTravel = travel
            this.currentFleetsGround = null
            this.currentFleetsOrbitting = null
            
            this.currentTravelIndex = index
            this.currentFleetsOrbittingIndex = -1
        },
        
        setCurrentFleetsGround(planet) {
            
            this.currentTravel = null
            this.currentFleetsGround = planet
            this.currentFleetsOrbitting = null
            
            this.currentTravelIndex = -1
            this.currentFleetsOrbittingIndex = -1
        },
        
        setCurrentFleetsOrbitting(index, fleet) {
            
            this.currentTravel = null
            this.currentFleetsGround = null
            this.currentFleetsOrbitting = fleet
            
            this.currentTravelIndex = -1
            this.currentFleetsOrbittingIndex = index
        },
        
        //---
        
        showToast(text, type) {
        
            if (this.toastTimeout) {
                clearTimeout(this.toastTimeout)
            }
            
            this.toastText = text
            this.toastType = type
            
            const self = this
            this.toastTimeout = setTimeout(function() { self.toastText = null }, 3e3)
        },
        
        //---
        
        togglePause() {
        
            this.game.paused = !this.game.paused
            
            if (this.game.paused == false) this.showToast("Game resumed!", "info")
            else this.showToast("Game paused!", "info")
        },
        
        toggleBonus() {
        
            this.game.bonusActive = !this.game.bonusActive
            
            if (this.game.bonusActive == false) this.showToast("Bonus Time disabled! Game speed normal!", "info")
            else this.showToast("Bonus Time enabled! Game speed x5!", "success")
        },
        
        manualSave() {
        
            this.save()
            this.showToast("Game saved in local storage!", "info")
        },
       
        makeBattle(attackFleet, enemyFleet) {
        
            let battleReport = attackFleet.battle(enemyFleet)
            
            this.popupText = battleReport.toHtml(this)
            
            this.popupTextOkAction = function() {
            
                if (attackFleet.getShipCount() <= 0) {
                    this.currentFleetsOrbitting = null
                }
            
                this.closePopup()
            }
            
            this.popupTextOk = "Close"
            this.popupTextCancel = null
        },
        
        //---

        start() {
            
            this.game.buildData()
            
            try {
            
                if (this.load() == false) {
                    this.game.initStartingData()
                }
                
                this.currentPlanet = this.game.planets["promision"]
                this.currentNebula = this.game.nebulas["perseus"]
                
                this.update()
                
                window.onbeforeunload = () => {
                
                    if (this.game.resetInProgress == false) {
                        this.save()
                    }
                }
                
                this.rafHandle = requestAnimationFrame(this.update)
                this.saveInterval = setInterval(() => { this.save() }, this.autoSaveDelay)
                
                this.showPlanetPage(this.currentPlanet)
                
                this.started = true
            }
            catch (error) {
                
                this.error = error
                
                console.error(error)
            }
        },

        update() {
            
            let currentTime = new Date().getTime()
            
            let deltaTime = currentTime - this.lastFrameTimeMs
            if (deltaTime >= 1000 / this.fps) {
            
                this.lastFrameTimeMs = currentTime                
                this.game.mainLoop(deltaTime)
            
                if (this.game.tutorialActive == true) {                
                    this.processTutorial()
                }
            }
            
            this.rafHandle = requestAnimationFrame(this.update)
        },
        
        processTutorial() {
        
            for (var tutId in this.game.tutorials) {
                if (this.game.tutorials[tutId].done == false) {
                    break
                }
            }
            
            let tutDone = null
            
            let tut = this.game.tutorials[tutId]
            if (tut.ref.check(this) == true) {
            
                tut.ref.action(this)
                tutDone = tut
            }
            
            if (tutDone != null) {
            
                this.popupText = tutDone.ref.text
                
                this.popupTextOkAction = function() {
                
                    if (tutDone.ref.id == "tutLast") {
                        this.game.tutorialActive = false
                    }
                    
                    tutDone.done = true
                    this.closePopup()
                }
                
                if (tutDone.ref.id == "tut6") {
                
                    this.popupTextOk = null
                    this.popupTextCancel = "Got it"
                }
                else if (tutDone.ref.id == "tutLast") {
                
                    this.popupTextOk = "End Tutorial"
                    this.popupTextCancel = null
                }
                else {
                
                    this.popupTextOk = "Continue"
                    this.popupTextCancel = "Disable Tutorial"
                }
                
                this.popupTextCancelAction = function() {
                
                    this.game.tutorialActive = false
                    this.closePopup()
                }
            }
        },
        
        load() {
            
            let loadedData = localStorage.getItem(this.localStorageName)
            if (loadedData && loadedData !== null && loadedData.length % 4 == 0) {
            
                let text = LZString.decompressFromBase64(loadedData)
                if (!text) return console.warn('Load failed')
                loadedData = JSON.parse(text)
                
                this.game.loadFromData(loadedData)
                
                if (loadedData.lastFrameTimeMs) {
                
                    let delta = new Date().getTime() - loadedData.lastFrameTimeMs
                    this.game.bonus.count += delta
                }
                
                return true
            }
            
            return false
        },

        save() {
            
            let savedData = this.game.getSavedData()
            
            savedData.lastFrameTimeMs = this.lastFrameTimeMs
            
            let text = JSON.stringify(savedData)
            let compressed = LZString.compressToBase64(text)
            localStorage.setItem(this.localStorageName, compressed)
        },

        importGameData() {

            if (!this.importExportData || !this.importExportData.trim()) return this.showToast("No data to import!", "error")
            if (this.importExportData.length % 4 !== 0) return this.showToast("Data corrupted!", "error")
            
            this.game.resetInProgress = true
            
            localStorage.setItem(this.localStorageName, this.importExportData)
            window.location.reload()
        },

        resetGameData() {
            
            this.game.resetInProgress = true
            
            localStorage.removeItem(this.localStorageName)
            window.location.reload()
        },
    },

    created() {

        let txt = navigator.userAgent || navigator.vendor || window.opera
        if (/(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino/i.test(txt))
            this.isMobile = true
            
        setTimeout(() => { this.start() }, this.minLoadingTimerMS)
    },

    beforeDestroy() {
        
        if (this.toastTimeout) {
            clearTimeout(this.toastTimeout)
        }
        
        clearInterval(this.saveInterval)
        cancelAnimationFrame(this.rafHandle)
    },
}
</script>
