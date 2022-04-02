<template>
    <div class="h-100 w-100">
    
        <div v-if="isMobile == true" class="position-absolute bg-grid top-0 bottom-0 start-0 end-0 d-flex align-items-center justify-content-center">
            <div class="p-3">
                <div class="row g-2">
                    <div class="col-12 text-center"><img :src="require(`~/assets/ui/logo.png`)" width="48px" class="rounded" /></div>
                    <div class="col-12 text-white text-center h5">HoG by FG</div>
                    <div class="col-12 text-warning text-center">Your device is not supported for the moment.</div>
                    <div class="col-12 text-normal text-center">To be informed when your device will be supported and new features will be ready, please join Discord server.</div>
                    <div class="col-12 text-normal text-center"><a href='https://discord.gg/3UkgeeT9CV' target='_blank' class='btn text-white'>Join Discord server</a></div>                
                </div>
            </div>
        </div>
        
        <div v-if="isMobile == false && started == false" class="position-absolute bg-grid top-0 bottom-0 start-0 end-0 d-flex align-items-center justify-content-center">
            <div class="p-3">
                <div class="row g-2">
                    <div class="col-12 text-center"><img :src="require(`~/assets/ui/logo.png`)" width="48px" class="rounded" /></div>
                    <div class="col-12 text-white text-center h5">HoG by FG</div>
                    <div class="col-12 flicker text-warning text-center">Initializing game...</div>
                </div>
            </div>
        </div>

        <div v-if="isMobile == false && started == true" class="position-absolute bg-grid top-0 bottom-0 start-0 end-0">
        
            <div v-if="popupText" class="position-absolute top-0 bottom-0 start-0 end-0 bg-3 d-flex align-items-center justify-content-center" style="z-index:100;">
                <div class="bg-2 rounded border p-3" style="width:380px;">
                    <div class="row g-3">
                        <div class="col-12 text-center" v-html="popupText"></div>
                        <div v-if="popupCancelText" class="col-6 text-start">
                            <button type="button" class="btn" @click="popupCancelAction()">
                                <span>{{ popupCancelText }}</span>
                            </button>
                        </div>
                        <div v-if="popupOkText" class="col-6 text-end">
                            <button type="button" class="btn" @click="popupOkAction()">
                                <span>{{ popupOkText }}</span>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        
            <div v-if="toastText" class="position-absolute start-0 end-0" style="bottom:60px; z-index:100;">
				<div class="d-flex justify-content-center">
                    <div class="toast show toast-body bg-2 text-center">
                        <span :class="{ 'text-normal':toastType == 'info', 'text-danger':toastType == 'error' }" v-html="toastText"></span>
                    </div>
                </div>
			</div>
            
            <div class="w-100 h-100 d-flex align-items-center justify-content-center">
                <div class="w-100 h-100 position-relative border rounded" style="max-width:1160px; max-height:728px;">
                    
                    <div class="position-absolute top-0 start-0 end-0 border-bottom bg-1 d-flex align-items-center" style="height:50px;">
                        <div class="col-auto px-3" style="width:275px;">
                            <div class="row gx-2">
                                <span class="col-auto text-normal">Influence</span>
                                <span class="col-auto text-white">{{ influence.toLocaleString() }}</span>
                            </div>
                        </div>
                        <div class="col">
                            <div class="row justify-content-center">
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':getFleetsPageState() == 'active', 'text-gray':getFleetsPageState() == 'locked' }" style="width:80px;" @click="showFleetsPage()">
                                    <div class="h5"><i class="fas fa-fw fa-space-shuttle"></i></div>
                                    <div class="small">Fleets</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':getMapPageState() == 'active', 'text-gray':getMapPageState() == 'locked' }" style="width:80px;" @click="showMapPage()">
                                    <div class="h5"><i class="fas fa-fw fa-map"></i></div>
                                    <div class="small">Map</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':currentPage == 'research' }" style="width:80px;" @click="showResearchPage()">
                                    <div class="h5"><i class="fas fa-fw fa-atom"></i></div>
                                    <div class="small">Research</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':getDiplomacyPageState() == 'active', 'text-gray':getDiplomacyPageState() == 'locked' }" style="width:80px;" @click="showDiplomacyPage()">
                                    <div class="h5"><i class="fas fa-fw fa-hands-helping"></i></div>
                                    <div class="small">Diplomacy</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':getTournamentPageState() == 'active', 'text-gray':getTournamentPageState() == 'locked' }" style="width:80px;" @click="showTournamentPage()">
                                    <div class="h5"><i class="fas fa-fw fa-trophy"></i></div>
                                    <div class="small">Tournament</div>
                                </button>
                                <button type="button" class="col-auto rounded-0 btn lh-1" :class="{ 'text-white bg-2':currentPage == 'overview' }" style="width:80px;" @click="showOverviewPage()">
                                    <div class="h5"><i class="fas fa-fw fa-globe"></i></div>
                                    <div class="small">Overview</div>
                                </button>
                            </div>				
                        </div>				
                        <div class="col-auto px-3" style="width:275px;">
                            <div class="row align-items-center">
                                <div class="col">
                                    <div class="row gx-2 justify-content-end">
                                        <span class="col-auto text-normal">Player Name</span>
                                        <span class="col-auto text-white">Player</span>
                                    </div>
                                    <div class="row gx-2 justify-content-end">
                                        <span class="col-auto text-normal">Year</span>
                                        <span class="col-auto text-white">{{ year }}</span>
                                        <span class="col-auto text-normal">Day</span>
                                        <span class="col-auto text-white">{{ day }}</span>
                                    </div>
                                </div>
                                <button v-if="paused == false" type="button" class="col-auto btn lh-1" style="width:65px;" @click="togglePause()">
                                    <div class="h5"><i class="fas fa-fw fa-pause"></i></div>
                                    <div class="small">Pause</div>
                                </button>
                                <button v-if="paused == true" type="button" class="col-auto btn lh-1" style="width:65px;" @click="togglePause()">
                                    <div class="h5"><i class="fas fa-fw fa-play"></i></div>
                                    <div class="small">Resume</div>
                                </button>
                            </div>
                        </div>
                    </div>
                    
                    <div class="position-absolute bottom-0 start-0 end-0 border-top bg-1 d-flex align-items-center" style="height:50px;">
                        <div class="col-auto px-3" style="width:275px;">
                            <div class="row gx-2 align-items-baseline">
                                <span class="col-auto text-normal">Research Points</span>
                                <span class="col-auto text-white"><FormatNumber :value="researchPoint.count" /></span>
                                <span class="col-auto small text-success">+<FormatNumber :value="researchPoint.prod" /> <small class="opacity-75">/s</small></span>
                            </div>
                        </div>
                        <div class="col">
                            <div class="row justify-content-center" style="display: flex;">
                                <button id="b_extraction_icon" type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'extraction' }" style="width:75px;" @click="showExtractionPage()">
                                    <div class="h5"><i class="fas fa-fw fa-hard-hat"></i></div>
                                    <div class="small">Extraction</div>
                                </button>
                                <button id="b_production_icon" type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'production' }" style="width:75px;" @click="showProductionPage()">
                                    <div class="h5"><i class="fas fa-fw fa-industry"></i></div>
                                    <div class="small">Production</div>
                                </button>
                                <button id="b_energy_icon" type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'energy' }" style="width:75px;" @click="showEnergyPage()">
                                    <div class="h5"><i class="fas fa-fw fa-bolt"></i></div>
                                    <div class="small">Energy</div>
                                </button>
                                <button id="b_res_icon" type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'labs' }" style="width:75px;" @click="showLabsPage()">
                                    <div class="h5"><i class="fas fa-fw fa-flask"></i></div>
                                    <div class="small">Labs</div>
                                </button>
                                <button id="b_other_icon" type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':currentPage == 'others' }" style="width:75px;" @click="showOthersPage()">
                                    <div class="h5"><i class="fas fa-fw fa-building"></i></div>
                                    <div class="small">Others</div>
                                </button>
                                <button id="b_other_icon" type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':getShipyardPageState() == 'active', 'text-gray':getShipyardPageState() == 'locked' }" style="width:75px;" @click="showShipyardPage()">
                                    <div class="h5"><i class="fas fa-fw fa-rocket"></i></div>
                                    <div class="small">Shipyard</div>
                                </button>
                                <button id="b_other_icon" type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':getMarketPageState() == 'active', 'text-gray':getMarketPageState() == 'locked', 'text-danger':getMarketPageState() == 'forbidden' }" style="width:75px;" @click="showMarketPage()">
                                    <div class="h5"><i class="fas fa-fw fa-store"></i></div>
                                    <div class="small">Market</div>
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
                        <div class="pt-4 text-center">
                            <span class="text-danger">Page not implemented yet</span>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'map'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col p-3 position-relative" :class="{ 'bg-perseus':currentNebula.id == 'perseus' }" style="overflow-y:auto;">
                                <MapPlanet v-for="planet in getNebulaPlanets()" :key="planet.id" :planet="planet" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'research'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col p-3" style="overflow-y:auto;">
                                <div class="text-center mb-3 h5 text-primary">Tech Tree</div>
                                <div class="row g-1">
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <ResearchSummary :research="researches['astronomy']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <i class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <ResearchSummary :research="researches['science']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col"></div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <ResearchSummary :research="researches['mineralogy']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <i v-if="isResearchVisible('vulcan')" class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <ResearchSummary v-if="isResearchVisible('vulcan')" :research="researches['vulcan']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col text-center">
                                                <div v-if="isResearchVisible('military')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('military')" :research="researches['military']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="isResearchVisible('artificial_intelligence')" :research="researches['artificial_intelligence']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('artificial_intelligence')" class="text-normal fas fa-fw fa-chevron-circle-left"></i>
                                            </div>
                                            <div class="col">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="isResearchVisible('electronics')" :research="researches['electronics']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('electronics')" class="text-normal fas fa-fw fa-chevron-circle-left"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('material')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('material')" :research="researches['material']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('ice')" class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="isResearchVisible('ice')" :research="researches['ice']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <div v-if="isResearchVisible('artofwar')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('artofwar')" :research="researches['artofwar']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('halean')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('halean')" :research="researches['halean']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('nuclear')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('nuclear')" :research="researches['nuclear']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('chemical')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('chemical')" :research="researches['chemical']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('hydro')" class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('hydro')" class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="isResearchVisible('hydro')" :research="researches['hydro']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <div v-if="isResearchVisible('karan_artofwar')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('karan_artofwar')" :research="researches['karan_artofwar']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('quantum')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('quantum')" :research="researches['quantum']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('secret')" class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('secret')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('secret')" :research="researches['secret']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('secret')" class="text-normal fas fa-fw fa-chevron-circle-left"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('mk_tech')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('mk_tech')" :research="researches['mk_tech']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('environment')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('environment')" :research="researches['environment']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <div v-if="isResearchVisible('xiran_artofwar')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('xiran_artofwar')" :research="researches['xiran_artofwar']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('karan_nuclear')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('karan_nuclear')" :research="researches['karan_nuclear']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('space_mining')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('space_mining')" :research="researches['space_mining']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('rhodium')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('rhodium')" :research="researches['rhodium']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('ammonia_chemistry')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('ammonia_chemistry')" :research="researches['ammonia_chemistry']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col"></div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                             <div class="col">
                                                <div v-if="isResearchVisible('protohalean_science')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('protohalean_science')" :research="researches['protohalean_science']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('darkmatter_science')" class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('darkmatter_science')" class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="isResearchVisible('darkmatter_science')" :research="researches['darkmatter_science']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('osmium')" class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('osmium')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('osmium')" :research="researches['osmium']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col"></div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px;overflow-y:auto;">
                                <ResearchDetails v-if="currentResearch" :research="currentResearch" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'diplomacy'" class="page">
                        <div class="pt-4 text-center">
                            <span class="text-danger">Page not implemented yet</span>
                        </div>
                    </div>

                    <div v-if="currentPage == 'tournament'" class="page">
                        <div class="pt-4 text-center">
                            <span class="text-danger">Page not implemented yet</span>
                        </div>
                    </div>

                    <div v-if="currentPage == 'overview'" class="page p-3">
                        <div class="row g-3">
                            <div class="col-12 text-center">
                                <span class="h5 text-primary">Planets under control</span>
                            </div>
                            <div class="col-12">
                                <div class="row g-1 align-items-center justify-content-center">
                                    <div v-for="(planet, key) of humanPlanets" :key="key" class="col-3">
                                        <button type="button" class="w-100 btn bg-1 rounded border" @click="showPlanetPage(planet)">
                                            <div class="row g-2 align-items-center">
                                                <div class="col-auto">
                                                    <img :src="require(`~/assets/planets/${key}.png`)" width="24px" />
                                                </div>
                                                <div class="col">
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
                                        <div class="h5" :class="{ 'text-normal':currentPlanet.civId == 'human', 'text-danger':currentPlanet.civId != null && currentPlanet.civId != 'human', 'text-gray':currentPlanet.civId == null }" >{{ $t('planetName_' + currentPlanet.id) }}</div>
                                    </div>
                                    <PlanetInfo :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <div class="col-12">
                                        <div v-for="(prod, key) of currentPlanetProds" :key="key" class="row gx-2">
                                            <span class="col text-normal">{{ $t('resName_' + key) }}</span>
                                            <span class="col-auto" :class="{ 'text-white':prod == 1, 'text-danger':prod < 1, 'text-success':prod > 1 }"><small class="opacity-75">x</small> {{ prod.toFixed(2) }}</span>
                                        </div>
                                    </div>
                                </div>
                            </div>               
                            <div class="col d-flex flex-column">
                                <div class="pt-3">
                                    <div class="row gx-2 justify-content-center align-items-center">
                                        <span class="col-auto text-white">Controlled by</span>
                                        <button type="button" class="col-auto btn py-0 d-flex align-items-center">
                                            <img :src="require(`~/assets/civis/${currentPlanet.civId}.png`)" width="32px" />
                                            <span class="ms-3 h5">{{ $t('civName_' + currentPlanet.civId) }}</span>
                                        </button>
                                    </div>
                                </div>
                                <div :class="'flex-fill d-flex align-items-center bg-' + currentPlanet.id">
                                    <button v-if="currentPlanet.civId == 'human' && humanPlanetCount > 1" type="button" id="arrow_left" class="btn">
                                        <i class="fas fa-fw fa-chevron-circle-left"></i>
                                    </button>
                                    <div class="col"></div>
                                    <button v-if="currentPlanet.civId == 'human' && humanPlanetCount > 1" type="button" id="arrow_right" class="btn">
                                        <i class="fas fa-fw fa-chevron-circle-right"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <div class="col-12 text-center">
                                        <span class="h5 text-primary">Status</span>
                                    </div>
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                        </div>
                    </div>

                    <div v-if="currentPage == 'extraction'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3" style="width:275px;overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3">
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('extraction')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px;overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'production'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3" style="width:275px;overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3">
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('production')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px;overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'energy'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3" style="width:275px;overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3">
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('energy')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px;overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'labs'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3" style="width:275px;overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3">
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('research')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px;overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'others'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3" style="width:275px;overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3">
                                <div v-if="Object.keys(getCurrentPlanetBuildings('other')).length < 1" class="text-center"><span class="text-gray">There is no building to show</span></div>
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('other')" :key="key" :building="building" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px;overflow-y:auto;">
                                <BuildingDetails v-if="currentBuilding" :building="currentBuilding" />
                            </div>
                        </div>
                    </div>

                    <div v-if="currentPage == 'shipyard'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3" style="width:275px;overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3">
                                <div v-if="Object.keys(getCurrentPlanetShips()).length < 1" class="text-center"><span class="text-gray">There is no ship to show</span></div>
                                <ShipSummary v-for="(ship, key) of getCurrentPlanetShips()" :key="key" :ship="ship" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3" style="width:275px;overflow-y:auto;">
                                <ShipDetails v-if="currentShip" :ship="currentShip" />
                            </div>
                        </div>
                    </div>

                    <div v-if="currentPage == 'market'" class="page">
                        <div class="pt-4 text-center">
                            <span class="text-danger">Page not implemented yet</span>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'settings'" class="page p-4">
                        <div class="row g-4 justify-content-center">
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
var resourcesDef = [

    { id: "iron",     value: 16, },
    { id: "steel",    value: 4,  },
    { id: "titanium", value: 3,  },
    { id: "silicon",  value: 11, },
    { id: "graphite", value: 7,  },
    { id: "fuel",     value: 6,  },
    { id: "methane",  },
    { id: "uranium",  reqs: { mineralogy: 4 }, },
    { id: "sand",     reqs: { mineralogy: 4 }, },
]

class Resource {

    constructor(def) {
    
        this.id = def.id
        
        this.prod = 0
        this.count = 0
    }
}

var buildingsDef = [

    { id: "mine",                                      type: "extraction", costs:{ iron:{ count: 10, mult: 1.2 }, steel:{ count: 9.75e-4, mult: 1.3 }, titanium:{ count: 2.1e-12, mult: 1.5 }}, prods:{ iron: 2 }, },
    { id: "methaneExtractor",                          type: "extraction", costs:{ iron:{ count: 100, mult: 1.2 }, steel:{ count: .1, mult: 1.3 }, titanium:{ count: 3e-5, mult: 1.5 }}, prods:{ methane: 1 }, },
    { id: "graphiteExtractor",                         type: "extraction", costs:{ iron:{ count: 500, mult: 1.2 }, steel:{ count: 4e-4, mult: 1.3 }, titanium:{ count: 3e-5, mult: 1.5 }}, prods:{ graphite: 1 }, },
    { id: "metalCollector",    reqs:{ mineralogy: 4 }, type: "extraction", costs:{ steel:{ count: 2e3, mult: 1.25 }, titanium:{ count: .9, mult: 1.35 }}, prods:{ titanium: 10, uranium: 1 }, energy: -50 },    
    { id: "sandQuarry",        reqs:{ mineralogy: 4 }, type: "extraction", costs:{ steel:{ count: 5e3, mult: 1.25 }, titanium:{ count: 1e3, mult: 1.35 }}, prods:{ sand: 1 }, energy: -80 },
    
    { id: "methaneProcesser",                          type: "production", costs:{ iron:{ count: 100, mult: 1.1 }, steel:{ count: .25, mult: 1.2 }, titanium:{ count: 2e-4, mult: 1.3 }}, prods:{ fuel: 1, methane: -2 }, },
    { id: "foundry",                                   type: "production", costs:{ iron:{ count: 1e3, mult: 1.1 }, steel:{ count: .48, mult: 1.2 }, titanium:{ count: .01, mult: 1.3 }}, prods:{ steel: 2, iron: -2, graphite: -1, fuel: -2 }, },
    
    { id: "smallGenerator",                            type: "energy",     costs:{ iron:{ count: 2e3, mult: 1.15 }, steel:{ count: 100, mult: 1.27 }, titanium:{ count: .17, mult: 1.35 }}, prods:{ fuel: -3 }, energy: 20 },
    
    { id: "laboratory",                                type: "research",   costs:{ iron:{ count: 1e3, mult: 2 }, steel:{ count: 200, mult: 3 }, titanium:{ count: .01, mult: 4 }}, energy: -5, researchPoint: 4 },
    
    { id: "shipyard",          reqs:{ astronomy: 1 },  type: "other",      costs:{ steel:{ count: 5e3, mult: 2 }, titanium:{ count: 1e3, mult: 3.2 }}, },
    { id: "tradehub",          reqs:{ astronomy: 5 },  type: "other",      costs:{ steel:{ count: 5e4, mult: 2 }, titanium:{ count: 1e4, mult: 3.2 }}, },
]

class Building {

    constructor(def) {
    
        this.id = def.id
        this.type = def.type
        this.costs = def.costs
        this.prods = def.prods
        this.energy = def.energy || 0
        this.researchPoint = def.researchPoint || 0        
        
        this.needs = {}
        for (let rId in this.prods) this.needs[rId] = false
        
        this.count = 0
        this.queue = 0
        this.active = true
        this.stoppedDelay = 0
    }
    
    toggleActive() { this.active = !this.active }
    
    hasNeeds() {
    
        for (let rId in this.needs) if (this.needs[rId] == true) return true
        return false
    }
}

var shipsDef = [

    { id: "vitha", shipyardLevel: 1, costs:{ iron: 5e4, steel: 8e4, titanium: 5e3 }, },
]

class Ship {

    constructor(def) {
    
        this.id = def.id
        this.costs = def.costs
        this.shipyardLevel = def.shipyardLevel
        
        this.count = 0
    }
}

var planetsDef = [
    {
        id: "promision", civId: "human", nebula: "perseus", influence: 1, x: 64, y: 64, type: "terrestrial", radius: 6833, temp: 22, atmos: "oxygen", orbit: 1,
        prods:{ iron: 1, graphite: 1, titanium: 1, silicon: 1, uranium: 1, methane: 1, sand: .5 },
    }, 
]

class Planet {

    constructor(def) {
    
        this.id = def.id
        this.civId = def.civId
        this.nebula = def.nebula
        this.influence = def.influence
        this.x = def.x
        this.y = def.y
        this.type = def.type
        this.radius = def.radius
        this.temp = def.temp
        this.atmos = def.atmos
        this.orbit = def.orbit
        this.prods = def.prods
        
        this.resources = {}
        resourcesDef.forEach(def => { this.resources[def.id] = new Resource(def) })
        
        this.energy = { prod: 0, consum: 0, }
        this.researchPoint = { prod: 0, }
        
        this.buildings = {}
        buildingsDef.forEach(def => { this.buildings[def.id] = new Building(def) })        
        
        this.ships = {}
        shipsDef.forEach(def => { this.ships[def.id] = new Ship(def) })        
    }
    
    emptyQueue(bId) { this.buildings[bId].queue = 0 }
    
    queueBuilding(bId, count) { this.buildings[bId].queue += count }
    
    destroyBuilding(bId, count) {
    
        this.buildings[bId].count -= count
        
        let refunds = this.getBuildingRefund(bId, count)
        for (let rId in refunds)
            this.resources[rId].count += refunds[rId]
    }
    
    produce(delta) {
    
        for (let rId in this.resources) this.resources[rId].prod = 0        
        this.researchPoint.prod = 0
        
        let energy = { prod: 0, consum: 0 }
        
        for (let bId in this.buildings) {
            let building = this.buildings[bId]
            
            if (building.count > 0 && building.active) {
                
                if (building.stoppedDelay > 10) building.stoppedDelay = 0
                else if (building.stoppedDelay > 0) building.stoppedDelay += delta
                
                if (building.stoppedDelay <= 0) {
                                    
                    if (this.canProduce(building.id) == true) {
                    
                        let energyCoeff = this.getEnergyCoeff()
                        if (building.energy >= 0) energyCoeff = 1
                        
                        for (let rId in building.prods) {
                            this.resources[rId].prod += building.prods[rId] * building.count * energyCoeff
                            if (this.prods[rId]) this.resources[rId].prod *= this.prods[rId]
                        }
                        
                        this.researchPoint.prod += building.researchPoint * building.count * energyCoeff
                        
                        if (building.energy > 0) energy.prod += building.energy * building.count
                        else if (building.energy < 0) energy.consum += building.energy * building.count
                    }
               }
            }
        }
        
        for (let rId in this.resources) {
            this.resources[rId].count += this.resources[rId].prod * delta
            if (this.resources[rId].count < 0) this.resources[rId].count = 0
        }
        
        this.energy = energy
    }
    
    getRawProduction(bId, count) {
    
        let ret = {}
        
        let building = this.buildings[bId]
        
        if (building.energy > 0) ret["energy"] = building.energy * count
        if (building.researchPoint > 0) ret["researchPoint"] = building.researchPoint * count
        
        for (let rId in building.prods) {
            ret[rId] = building.prods[rId] * count
            if (this.prods[rId]) ret[rId] *= this.prods[rId]
        }

        if (building.energy < 0) ret["energy"] = building.energy * count
        
        return ret
    }
        
    getEnergyCoeff() {
    
        let ret = 1
        
        if (this.energy.consum != 0) ret = Math.abs(this.energy.prod / this.energy.consum)
        if (ret > 1) ret = 1
        if (ret < 0) ret = 0
        
        return ret
    }
    
    getBuildingCost(bId, count) {
    
        let ret = {}
        
        let building = this.buildings[bId]
        for (let rId in building.costs) {
        
            let t1 = Math.pow(building.costs[rId].mult, building.count)
            ret[rId] = building.costs[rId].count * t1
            
            for (let i = 1; i < count; i++) {
            
                t1 *= building.costs[rId].mult
                ret[rId] += building.costs[rId].count * t1
            }
        }
        
        return ret
    }
    
    getBuildingRefund(bId, count) {
    
        let ret = {}
        
        for (let rId in this.buildings[bId].costs) {
            ret[rId] = 0
            for (let n = 0; n < count; n++)
                ret[rId] += (this.buildings[bId].costs[rId].count * Math.pow(this.buildings[bId].costs[rId].mult, this.buildings[bId].count - n - 1))
        }

        for (let rId in ret) ret[rId] /= 2
        
        return ret
    }
    
    canBuy(costs) {
    
        let ret = true
        
        for (let rId in costs)
            if (costs[rId] >= 1 && this.resources[rId].count < costs[rId]) {            
                ret = false
                break
            }
            
        return ret
    }

    canProduce(bId) {
    
        let ret = true
        
        let building = this.buildings[bId]
        
        if (building.type != "extraction") {
            for (let rId in building.prods) {
                if (this.resources[rId].count + (building.count * building.prods[rId]) < 0) {
                    ret = false
                    building.needs[rId] = true
                    building.stoppedDelay = 1
                }
                else {
                    building.needs[rId] = false
                }
            }
        }
        
        return ret
    }
    
    isShipUnlocked(sId) {
    
        let ret = true
        
        let ship = this.ships[sId]
        let shipyard = this.buildings["shipyard"]
        
        if (shipyard.count < ship.shipyardLevel) ret = false
        
        return ret
    }
    
    getShipCost(sId, count) {
    
        let ret = {}
        
        let ship = this.ships[sId]
        for (let rId in ship.costs)        
            ret[rId] = ship.costs[rId] * count
        
        return ret
    }
    
    buildShip(sId, count) {
    
        let ship = this.ships[sId]
        for (let rId in ship.costs)        
            this.resources[rId].count -= ship.costs[rId] * count
        
        ship.count += count
    }
}

var nebulasDef = [

    { id: "perseus", },
]

class Nebula {

    constructor(def) {
    
        this.id = def.id
        
        this.planets = {}
    }
}

var researchesDef = [

    { id: "astronomy", desc: true, researchPoint: 3e3, mult: 4.3,
      shipBonuses: [
        { id:"vitha", stats:[{ id: "speed", minLevel: 1, value: 12, }], },
      ],
      unlocks: [
        { level:1, buildings:["shipyard"], },
      ],
      isVisible(state) { return true },
    },
    { id: "science", researchPoint: 4e4, mult: 2.5, reqs:{ astronomy: 1 }, max: 64,
      buildingBonuses: [
        { id:"laboratory", researchPoint:{ minLevel: 1, value: 11, }, },
      ],
      isVisible(state) { return true },
    },
    
    { id: "mineralogy", researchPoint: 125, mult: 2.2,
      buildingBonuses: [
        { id:"mine", prods:[{ id: "iron", minLevel: 1, value: 25, maxLevel: 125, reduction: .2, }], },
        { id:"graphiteExtractor", prods:[{ id: "graphite", minLevel: 1, value: 12, }], },
      ],
      unlocks: [
        { level:4, buildings:["metalCollector", "sandQuarry"], },
      ],
      isVisible(state) { return true },
    },
    { id: "material", researchPoint: 500, mult: 2.2, reqs:{ mineralogy: 1 },
      isVisible(state) { return true },
      buildingBonuses: [
        { id:"foundry", prods:[{ id: "steel", minLevel: 1, value: 50, maxLevel: 100, reduction: .5, }], },
      ],
    },
    { id: "chemical", researchPoint: 1200, mult: 2.2, reqs:{ material: 1 },
      isVisible(state) { return state.isResearchUnlocked('material') },
      buildingBonuses: [
        { id:"methaneProcesser", prods:[{ id: "fuel", minLevel: 1, value: 25, }], },
      ],
    },
    
    { id: "ice",
      isVisible(state) { return false },
    },
    { id: "military",
      isVisible(state) { return false },
    },
    { id: "electronics",
      isVisible(state) { return false },
    },
    { id: "nuclear",
      isVisible(state) { return false },
    },
    { id: "environment",
      isVisible(state) { return false },
    },
    { id: "halean",
      isVisible(state) { return false },
    },
    { id: "artofwar",
      isVisible(state) { return false },
    },
    { id: "artificial_intelligence",
      isVisible(state) { return false },
    },
    { id: "vulcan",
      isVisible(state) { return false },
    },    
    { id: "hydro",
      isVisible(state) { return false },
    },
    { id: "rhodium",
      isVisible(state) { return false },
    },
    { id: "osmium",
      isVisible(state) { return false },
    },
    { id: "quantum",
      isVisible(state) { return false },
    },
    { id: "secret",
      isVisible(state) { return false },
    },
    { id: "karan_artofwar",
      isVisible(state) { return false },
    },
    { id: "space_mining",
      isVisible(state) { return false },
    },
    { id: "karan_nuclear",
      isVisible(state) { return false },
    },
    { id: "ammonia_chemistry",
      isVisible(state) { return false },
    },
    { id: "protohalean_science",
      isVisible(state) { return false },
    },
    { id: "mk_tech",
      isVisible(state) { return false },
    },
    { id: "darkmatter_science",
      isVisible(state) { return false },
    },
    { id: "xiran_artofwar",
      isVisible(state) { return false },
    },
]

class Research {

    constructor(def) {
    
        this.id = def.id
        this.max = def.max
        this.desc = def.desc
        this.reqs = def.reqs
        this.mult = def.mult
        this.unlocks = def.unlocks
        this.shipBonuses = def.shipBonuses
        this.researchPoint = def.researchPoint
        this.buildingBonuses = def.buildingBonuses
        
        this.level = 0
    }
    
    getCost() { return this.researchPoint * Math.pow(this.mult, this.level) }
    
    getBuildingBonuses() {
        
        if (!this.buildingBonuses) return null
        
        let ret = {}
        
        this.buildingBonuses.forEach(bonus => {
            ret[bonus.id] = {}
            
            if (bonus.prods) {
                ret[bonus.id].prods = {}
                
                bonus.prods.forEach(prod => {
                
                    if (prod.minLevel - 1 <= this.level) {
                    
                        let value = prod.value
                        if (prod.reduction) {
                            value -= prod.reduction * ((this.level + 1) - prod.minLevel)
                            if (this.level > prod.maxLevel) value = 0
                            if (value < 0) value = 0
                        }
                        
                        if (value != 0) ret[bonus.id].prods[prod.id] = value
                    }
                })
            }
            
            if (bonus.researchPoint && bonus.researchPoint.minLevel - 1 <= this.level) ret[bonus.id].researchPoint = bonus.researchPoint.value
        })
        
        return ret
    }
    
    getShipBonuses() {
        
        if (!this.shipBonuses) return null
        
        let ret = {}

        this.shipBonuses.forEach(bonus => {
            ret[bonus.id] = {}

            if (bonus.stats) {
                ret[bonus.id].stats = {}
                
                bonus.stats.forEach(stat => {
                
                    if (stat.minLevel - 1 <= this.level)
                        ret[bonus.id].stats[stat.id] = stat.value
                })
            }
        })
        
        return ret
    }
    
    getNextUnlock() {
        
        if (!this.unlocks) return null
        return this.unlocks.find(unlock => unlock.level == this.level + 1)
    }
    
    getFutureUnlocks() {
    
        if (!this.unlocks) return null
        return this.unlocks.filter(unlock => unlock.level > this.level + 1)
    }
}

var questsDef = [

    { id:"pirates_4", },
]

class Quest {

    constructor(def) {
    
        this.id = def.id
        
        this.done = false
    }
}

var tutorialsDef = [
    {
        id: "tut0",
        text: "<div class='text-primary text-center h5 mb-4'>Welcome Commander</div>" +
              "<div class='text-normal text-center'>You finally woke up after a long cryosleep. 232 years have passed since you boarded the Vitha, but finally you reached your new home <span class='text-white'>Promision</span>.</div>" +
              "<div class='mt-2 text-danger text-center'>This version is a rewriting/remake of the original game. It is still under development so bugs and data lost could happen!</div>" +
              "<div class='mt-2 text-warning text-center'>You can disable this tutorial. To open it again, click on the icon <i class='fas fa-fw fa-question-circle'></i> in the bottom-right corner of the screen</div>",
        check: function(state) { return true },
        action: function(state) { if (state.currentPage != "planet" || state.currentPlanet.id != "promision") state.showPlanetPage(state.planets['promision'] ) },
    },
    {
        id: "tut1",
        text: "<div class='text-primary text-center h5 mb-4'>Let's do a little briefing</div>" +
              "<div class='text-normal text-center'>In this page you can see basic infos about your planet.</div>" +
              "<div class='mt-2 text-normal text-center'>On the left you can see a list of resources that can be <span class='text-white'>extracted</span> on this planet, like <span class='text-white'>Iron</span>.</div>" +
              "<div class='mt-2 text-normal text-center'>Click on the icon <i class='fas fa-fw fa-dice-d20'></i> in the bottom menu to access the <span class='text-white'>Extraction</span> page.</div>",
        check: function(state) { return true },
        action: function(state) { if (state.currentPage != "planet" || state.currentPlanet.id != "promision") state.showPlanetPage(state.planets['promision'] ) },
    },
    {
        id: "tut2",
        text: "<div class='text-primary text-center h5 mb-4'>Let's extract Iron</div>" +
              "<div class='text-normal text-center'>In this page, you can construct buildings to extract resources. By clicking on the desired building, you can see more details about it.</div>" +
              "<div class='mt-2 text-normal text-center'>On the left you can see how many resources are being produced every second.</div>" +
              "<div class='mt-2 text-normal text-center'>Now click on the icon <i class='fas fa-fw fa-plus-circle'></i>1 on the right of <span class='text-white'>Mining Plant</span> to build 1 more.</div>",
        check: function(state) { return state.currentPage == "extraction" && state.currentPlanet.id == "promision" },
        action: function(state) { },
    },
    {
        id: "tut3",
        text: "<div class='text-primary text-center h5 mb-4'>Let's extract Iron</div>" +
              "<div class='text-normal text-center'>Perfect! You can now see on the right how <span class='text-white'>Iron</span> production has doubled!</div>" +
              "<div class='mt-2 text-normal text-center'>Keep building <span class='text-white'>Mining Plants</span>, until you reach 10 of them. Should only take few seconds!</div>",
        check: function(state) { return state.currentPlanet.buildings["mine"].count > 1 },
        action: function(state) { if (state.currentPage != "extraction" || state.currentPlanet.id != "promision") { state.currentPlanet = state.planets["promision"]; state.showExtractionPage(); } },
    },
    {
        id: "tut4",
        text: "<div class='text-primary text-center h5 mb-4'>Let's go further</div>" +
              "<div class='text-normal text-center'>Perfect! But <span class='text-white'>Iron</span> is not the only resource you will need.</div>" +
              "<div class='mt-2 text-normal text-center'>Let's build a <span class='text-white'>Methane Extractor</span> to extract <span class='text-white'>Methane</span>.</div>",
        check: function(state) { return state.currentPlanet.buildings["mine"].count > 9 },
        action: function(state) { if (state.currentPage != "extraction" || state.currentPlanet.id != "promision") { state.currentPlanet = state.planets["promision"]; state.showExtractionPage(); } },
    },
    {
        id: "tut5",
        text: "<div class='text-primary text-center h5 mb-4'>Let's go further</div>" +
              "<div class='text-normal text-center'>Great! But <span class='text-white'>Methane</span> alone is not that useful, we need <span class='text-white'>Fuel</span>.</div>" +
              "<div class='mt-2 text-normal text-center'>Click on the icon <i class='fas fa-fw fa-industry'></i> in the bottom menu to access the <span class='text-white'>Production</span> page.</div>",
        check: function(state) { return state.currentPlanet.buildings["methaneExtractor"].count > 0 },
        action: function(state) { if (state.currentPage != "extraction" || state.currentPlanet.id != "promision") { state.currentPlanet = state.planets["promision"]; state.showExtractionPage(); } },
    },

//    "<div class='text-primary text-center h5 mb-4'>Let's go further</div>" +
//    "In this interface you can construct buildings that transform raw resources, like iron and methane, into more useful and advanced resources."
//    "Let's construct a <span class='white_text'>Methane Processer</span> to convert methane into fuel.",
    
    {
        id: "tut6",
        text: "<div class='text-primary text-center h5 mb-4'>Tutorial in progress</div><div class='text-normal text-center'>Next steps of tutorial are under development.</div><div class='mt-2 text-normal text-center'>To be informed when new steps and new features will be ready, please join Discord server.</div><div class='mt-2 text-normal text-center'><a href='https://discord.gg/3UkgeeT9CV' target='_blank' class='btn text-white'>Join Discord server</a></div>",
        check: function(state) { return true },
        action: function(state) { },
    },
]

class Tutorial {

    constructor(def) {
    
        this.id = def.id
        this.text = def.text
        this.check = def.check
        this.action = def.action
        
        this.done = false
    }
}

import LZString from 'lz-string'

export default {
    
    data() {
        return {
            
            fps: 60,
            locale:'en',
            started:false,
            isMobile:false,
            rafHandle:null,
            autoSaveDelay: 30000,
            importExportData:null,
            localStorageName:'fghog',
            minLoadingTimerMS:1000,
            lastFrameTimeMs:new Date().getTime(),
            
            popupText: null,
            popupOkText: "Ok",
            popupOkAction: null,
            popupCancelText: "Cancel",
            popupCancelAction: null,
            
            toastText: null,
            toastType: 'info',
            
            //---
            
            days: 0,
            paused: false,
            tutorial: true,
            government: null,
            isResseting: false,
            researchPoint: { count:0, prod:0 },
            timeTravelCount: 0,
            
            currentPage:'planet',
            currentShip: null,
            currentPlanet: null,
            currentNebula: null,
            currentBuilding: null,
            currentResearch: null,
            
            currentShipCount: 1,
            currentBuildCount: 1,
            currentDestroyCount: 1,
            
            quests: {},
            planets: {},
            nebulas: {},
            tutorials: {},
            researches: {},
        }
    },
    
    computed: {
    
        influence() {
        
            let ret = 0
            
            for (let pId in this.planets)
                if (this.planets[pId].civId == 'human') ret += this.planets[pId].influence
                
            return ret
        },
        
        year() { return parseInt(this.days / 365) },
        
        day() { return parseInt(this.days) - 365 * this.year },
        
        humanPlanets() {
        
            let ret = {}
            
            for (let pId in this.planets)
                if (this.planets[pId].civId == 'human') ret[pId] = this.planets[pId]
                
            return ret
        },
        
        humanPlanetCount() { return Object.keys(this.humanPlanets).length },

        exportGameData() {

            let text = localStorage.getItem(this.localStorageName)
            return text
        },

        currentPlanetProds() {
        
            let ret = {}
            
            for (let rId in this.currentPlanet.prods)
                if (this.isResUnlocked(rId) && this.currentPlanet.prods[rId] > 0)
                    ret[rId] = this.currentPlanet.prods[rId]
            
            return ret
        },
    },
    
    methods: {
                
        getFleetsPageState() {
        
            if (this.currentPage == 'fleets') return 'active'
            if (this.researches["astronomy"].level > 0) return null
            else return 'locked'
        },
        
        showFleetsPage() {
        
            if (this.researches["astronomy"].level > 0) this.currentPage = "fleets"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        getMapPageState() {
        
            if (this.currentPage == 'map') return 'active'
            if (this.researches["astronomy"].level > 0) return null
            else return 'locked'
        },
        
        showMapPage() {
        
            if (this.researches["astronomy"].level > 0) this.currentPage = "map"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        showResearchPage() { this.currentPage = "research" },
        
        getDiplomacyPageState() {
        
            if (this.currentPage == 'diplomacy') return 'active'
            if (this.timeTravelCount > 0 || this.researches["astronomy"].level > 6) return null
            else return 'locked'
        },
        
        showDiplomacyPage() {
        
            if (this.timeTravelCount > 0 || this.researches["astronomy"].level > 6) this.currentPage = "diplomacy"
            else this.showToast("You must time travel once<br> or research Interstellar Travel level 7 first!", "error")
        },

        getTournamentPageState() {
        
            if (this.currentPage == 'tournament') return 'active'
            if (this.researches["astronomy"].level > 6) return null
            else return 'locked'
        },
        
        showTournamentPage() {
        
            if (this.researches["astronomy"].level > 6) this.currentPage = "tournament"
            else this.showToast("You must research Interstellar Travel level 7 first!", "error")
        },

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
        
        getShipyardPageState() {
        
            if (this.currentPage == 'shipyard') return 'active'
            if (this.researches["astronomy"].level > 0) return null
            else return 'locked'
        },
        
        showShipyardPage() {
        
            if (this.researches["astronomy"].level > 0) this.currentPage = "shipyard"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        getMarketPageState() {
        
            if (this.currentPage == 'market') return 'active'
            if (this.quests["pirates_4"].done && this.government != "merchant") return 'forbidden'
            else if (this.currentPlanet.nebula == "perseus")
                if (this.currentPlanet.buildings["tradehub"].count > 0) return null
                else return 'locked'
            else return 'forbidden'
        },

        showMarketPage() {
            
            if (this.quests["pirates_4"].done && this.government != "merchant") this.showToast("You have been banned from the market as a result of your allegiance to pirates!", "error")
            else if (this.currentPlanet.nebula == "perseus")
                if (this.currentPlanet.buildings["tradehub"].count > 0) this.currentPage = "market"
                else this.showToast("You must build at least one Trade Hub!", "error")
            else this.showToast("The trade hub is unable to contact a market in this nebula!", "error")
        },

        showSettingsPage() { this.currentPage = "settings" },
        
        setCurrentBuilding(building) { this.currentBuilding = building },
        
        setCurrentResearch(research) { this.currentResearch = research },
        
        setCurrentShip(ship) { this.currentShip = ship },
        
        showToast(text, type) {
        
            this.toastText = text
            this.toastType = type
            
            const self = this
            setTimeout(function() { self.toastText = null }, 3e3)
        },
        
        showHardResetPopup() {
        
            this.popupText = "<div class='h5 text-danger mb-2'>Hard Reset</div><span class='text-danger'>Are you sure you want to wipe your data?<br>You will lose ALL your progress!</span>"
            this.popupOkAction = function() { this.resetGameData(); this.closePopup(); }
            this.popupCancelAction = this.closePopup
        },
        
        closePopup() {
        
            this.popupText = null
            this.popupOkText = "Ok"            
            this.popupOkAction = null
            this.popupCancelText = "Cancel"
            this.popupCancelAction = null
        },
        
        togglePause() {
        
            this.paused = !this.paused
            
            if (this.paused == true) this.showToast("Game paused!", "info")
            else this.showToast("Game resumed!", "info")
        },
        
        showTutorial() {
        
            this.tutorial = true
            this.checkTutorial()
        },
        
        manualSave() {
        
            this.save()
            this.showToast("Game saved in local storage!", "info")
        },
        
        getCurrentPlanetBuildings(type) {
        
            let ret = {}
            
            for (let bId in this.currentPlanet.buildings)
                if (this.isBuildingUnlocked(bId) && this.currentPlanet.buildings[bId].type == type)
                    ret[bId] = this.currentPlanet.buildings[bId]
            
            return ret
        },
        
        getCurrentPlanetShips() {
        
            let ret = {}
            
            for (let sId in this.currentPlanet.ships)
                if (this.currentPlanet.isShipUnlocked(sId))
                    ret[sId] = this.currentPlanet.ships[sId]
                    
            return ret
        },
        
        levelUpResearch(rId) {
        
            if (this.canLevelUp(rId)) {
                
                let research = this.researches[rId]
                this.researchPoint.count -= research.getCost()
                
                this.applyResearch(rId)
            }
        },
        
        canLevelUp(rId) {
        
            let ret = true
            
            let research = this.researches[rId]
            if (research.getCost() > this.researchPoint.count) ret = false
                
            return ret
        },
        
        applyResearch(rId) {
        
            let research = this.researches[rId]
            
            let bonuses = research.getBuildingBonuses()
            for (let bId in bonuses) {
            
                for (let rId in bonuses[bId].prods)
                    for (let pId in this.planets)
                        this.planets[pId].buildings[bId].prods[rId] *= (100 + bonuses[bId].prods[rId]) / 100
                        
                if (bonuses[bId].researchPoint)
                    for (let pId in this.planets)
                        this.planets[pId].buildings[bId].researchPoint *= (100 + bonuses[bId].researchPoint) / 100
            }
            
            research.level += 1
        },
        
        //---
        
        isResUnlocked(rId) {
        
            let def = resourcesDef.find(def => def.id == rId)
            
            for (let tId in def.reqs)
                if (this.researches[tId].level < def.reqs[tId])
                    return false
            
            for (let qId in def.quests)
                if (this.quests[qId].done == false)
                    return false
                    
            return true
        },
        
        isBuildingUnlocked(bId) {
        
            let def = buildingsDef.find(def => def.id == bId)
            
            for (let tId in def.reqs)
                if (this.researches[tId].level < def.reqs[tId])
                    return false
                    
            return true
        },
        
        isResearchUnlocked(rId) {
            
            let ret = true
            
            let def = researchesDef.find(def => def.id == rId)
            if (def.reqs)
                for (let rId in def.reqs)
                    if (this.researches[rId].level < def.reqs[rId])
                        ret = false
            
            return ret
        },
        
        isResearchVisible(rId) {
        
            let def = researchesDef.find(def => def.id == rId)                    
            return def.isVisible(this)
        },
        
        getNebulaPlanets() {
        
            let ret = {}
            
            let range = this.researches['astronomy'].level
            
            for (let pId in this.planets) {
                let planet = this.planets[pId]
                if (planet.nebula == this.currentNebula.id && planet.orbit <= range)
                    ret[pId] = planet
            }
            
            return ret
        },
        
        produceResources(delta) {
            
            this.researchPoint.prod = 0
            
            for (let pId in this.planets)
                if (this.planets[pId].civId == 'human') {
                    this.planets[pId].produce(delta)
                    this.researchPoint.prod += this.planets[pId].researchPoint.prod
                }
                        
            this.researchPoint.count += this.researchPoint.prod * delta
        },
        
        buildBuildings() {
        
            for (let pId in this.planets)
                if (this.planets[pId].civId == 'human')
                    for (let bId in this.planets[pId].buildings)
                        for (let i = 0; i < this.planets[pId].buildings[bId].queue; i++) {
                        
                            let costs = this.planets[pId].getBuildingCost(bId, 1)
                            if (this.planets[pId].canBuy(costs) == true) {
                                
                                this.planets[pId].buildings[bId].count += 1
                                for (let rId in costs) if (costs[rId] > 1) this.planets[pId].resources[rId].count -= costs[rId]
                                
                                this.planets[pId].buildings[bId].queue -= 1
                            }
                            
                            else break
                        }
        },
        
        checkTutorial() {
            
            for (var tutId in this.tutorials)
                if (this.tutorials[tutId].done == false)
                    break
            
            let tut = this.tutorials[tutId]
            if (tut.check(this) == true && this.popupText == null) {
            
                tut.action(this)
                
                this.popupText = tut.text
                
                this.popupOkAction = function() {
                
                    if (tut.id == "tutLast") this.tutorial = false;
                    tut.done = true;
                    this.closePopup();
                }
                
                if (tut.id == "tut6") {
                
                    this.popupOkText = null
                    this.popupCancelText = "Got it"
                }
                else if (tut.id == "tutLast") {
                
                    this.popupOkText = "End Tutorial"
                    this.popupCancelText = null
                }
                else {
                
                    this.popupOkText = "Continue"
                    this.popupCancelText = "Disable Tutorial"
                }
                
                this.popupCancelAction = function() { this.tutorial = false; this.closePopup(); }
            }
        },
        
        //---
            
        init() {
        
            questsDef.forEach(def => { this.quests[def.id] = new Quest(def) })
            planetsDef.forEach(def => { this.planets[def.id] = new Planet(def) })
            nebulasDef.forEach(def => { this.nebulas[def.id] = new Nebula(def) })
            tutorialsDef.forEach(def => { this.tutorials[def.id] = new Tutorial(def) })
            researchesDef.forEach(def => { this.researches[def.id] = new Research(def) })
            
            for (let nId in this.nebulas) {
                let nebula = this.nebulas[nId]
                
                for (let pId in this.planets) {
                    let planet = this.planets[pId]
                    if (planet.nebula == nebula.id) nebula.planets[pId] = planet
                }
            }
            
            this.currentNebula = this.nebulas["perseus"]
            
            this.currentPlanet = this.planets["promision"]
            this.currentPlanet.buildings["mine"].count = 1
        },

        load() {
        
            let loadeddata = localStorage.getItem(this.localStorageName)
            if (loadeddata && loadeddata !== null && loadeddata.length % 4 == 0) {
            
                let text = LZString.decompressFromBase64(loadeddata)
                if (!text) return console.warn('Load failed')
                loadeddata = JSON.parse(text)
                
                this.days = loadeddata.days || 0
                this.paused = loadeddata.paused
                this.tutorial = loadeddata.tutorial
                this.government = loadeddata.government
                this.researchPoint = loadeddata.researchPoint || { count:0, prod:0 }
                this.timeTravelCount = loadeddata.timeTravelCount || 0
                
                for (let qId in this.quests) {            
                    let quest = loadeddata.quests[qId]                
                    if (quest) this.quests[qId].done = quest.done
                }
                
                for (let pId in this.planets) {
                    let planet = loadeddata.planets[pId]
                    if (planet) {
                        this.planets[pId].civId = planet.civId
                        
                        if (loadeddata.planets[pId].buildings)
                            for (let bId in this.planets[pId].buildings) {
                                let building = loadeddata.planets[pId].buildings[bId]
                                if (building) {
                                    this.planets[pId].buildings[bId].count = building.count
                                    this.planets[pId].buildings[bId].queue = building.queue
                                    this.planets[pId].buildings[bId].active = building.active
                                }
                            }
                        
                        if (loadeddata.planets[pId].resources)
                            for (let rId in this.planets[pId].resources) {
                                let resource = loadeddata.planets[pId].resources[rId]
                                if (resource) this.planets[pId].resources[rId].count = resource.count
                            }
                        
                        if (loadeddata.planets[pId].ships)
                            for (let sId in this.planets[pId].ships) {
                                let ship = loadeddata.planets[pId].ships[sId]
                                if (ship) this.planets[pId].ships[sId].count = ship.count
                            }
                    }
                }
            
                for (let tId in this.tutorials) {            
                    let tutorial = loadeddata.tutorials[tId]
                    if (tutorial) this.tutorials[tId].done = tutorial.done
                }
                
                for (let rId in this.researches) {            
                    let research = loadeddata.researches[rId]
                    if (research)
                        for (let i = 0; i < research.level; i++)
                            this.applyResearch(rId)
                }
            }
        },

        save() {
            
            let saveddata = {
            
                days: this.days,
                paused: this.paused,
                tutorial: this.tutorial,
                government: this.government,
                researchPoint: this.researchPoint,
                timeTravelCount: this.timeTravelCount,
                
                quests: {},
                planets: {},
                tutorials: {},
                researches: {},
            }
            
            for (let qId in this.quests) {            
                let quest = this.quests[qId]                
                saveddata.quests[qId] = { id: quest.id, done: quest.done, }
            }

            for (let pId in this.planets) {
                let planet = this.planets[pId]
                saveddata.planets[pId] = { id: planet.id, civId: planet.civId, buildings: {}, resources: {}, ships: {},  }
                
                for (let bId in this.planets[pId].buildings) {
                    let building = this.planets[pId].buildings[bId]
                    saveddata.planets[pId].buildings[bId] = { id: building.id, count: building.count, queue: building.queue, active: building.active }
                }
                
                for (let rId in this.planets[pId].resources) {
                    let resource = this.planets[pId].resources[rId]
                    saveddata.planets[pId].resources[rId] = { id: resource.id, count: resource.count }
                }
            
                for (let sId in this.planets[pId].ships) {            
                    let ship = this.planets[pId].ships[sId]                
                    saveddata.planets[pId].ships[sId] = { id: ship.id, count: ship.count, }
                }
            }
            
            for (let tId in this.tutorials) {            
                let tutorial = this.tutorials[tId]                
                saveddata.tutorials[tId] = { id: tutorial.id, done: tutorial.done, }
            }
            
            for (let rId in this.researches) {            
                let research = this.researches[rId]                
                saveddata.researches[rId] = { id: research.id, level: research.level, }
            }
            
            let text = JSON.stringify(saveddata)
            let compressed = LZString.compressToBase64(text)
            localStorage.setItem(this.localStorageName, compressed)
        },

        start() {
            
            this.init()
            this.load()
            this.update()
            
            window.onbeforeunload = () => { if (this.isResseting == false) this.save() }
            
            this.rafHandle = requestAnimationFrame(this.update)
            this.saveInterval = setInterval(() => { this.save() }, this.autoSaveDelay)
            
            this.showPlanetPage(this.currentPlanet)
            
            this.started = true
        },

        update() {
            
            let currentTime = new Date().getTime()
            
            let deltaTime = currentTime - this.lastFrameTimeMs
            if (deltaTime >= 1000 / this.fps) {
            
                this.lastFrameTimeMs = currentTime
                if (this.paused == false) {
                    
                    this.days += .1 * (deltaTime / 1000)
                    
                    this.produceResources(deltaTime / 1000)
                    this.buildBuildings()
                    
                    if (this.tutorial == true) this.checkTutorial()
                }
            }
            
            this.rafHandle = requestAnimationFrame(this.update)
        },

        importGameData() {

            if (!this.importExportData || !this.importExportData.trim()) return this.showToast("No data to import!", "error")
            if (this.importExportData.length % 4 !== 0) return this.showToast("Data corrupted!", "error")
            
            localStorage.setItem(this.localStorageName, this.importExportData)
            window.location.reload()
        },

        resetGameData() {
            
            this.isResseting = true
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
        
        clearInterval(this.saveInterval)
        cancelAnimationFrame(this.rafHandle)
    },
}
</script>
