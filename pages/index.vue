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
            
            <div class="w-100 h-100 d-flex align-items-center justify-content-center">
                <div class="w-100 h-100 position-relative border rounded" style="max-width:1160px; max-height:728px;">
                
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
                                <span :class="{ 'text-normal':toastType == 'info', 'text-danger':toastType == 'error', 'text-success':toastType == 'success' }" v-html="toastText"></span>
                            </div>
                        </div>
                    </div>
                    
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
                            <div class="row gx-2 align-items-center justify-content-end">
                                <div class="col-auto text-end">
                                    <div class="text-normal">Bonus Time</div>
                                    <div class="text-white"><FormatTime :value="bonusTime / 1000" /></div>
                                </div>
                                <button v-if="bonusActive == false" type="button" class="col-auto btn lh-1" :class="{ 'disabled':bonusTime <= 0 }" style="width:65px;" @click="toggleBonus()">
                                    <div class="h5"><i class="fas fa-fw fa-play"></i></div>
                                    <div class="small">Disabled</div>
                                </button>
                                <button v-if="bonusActive == true" type="button" class="col-auto btn lh-1" style="width:65px;" @click="toggleBonus()">
                                    <div class="h5"><i class="fas fa-fw fa-pause"></i></div>
                                    <div class="small">Enabled</div>
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
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':getShipyardPageState() == 'active', 'text-gray':getShipyardPageState() == 'locked' }" style="width:75px;" @click="showShipyardPage()">
                                    <div class="h5"><i class="fas fa-fw fa-rocket"></i></div>
                                    <div class="small">Shipyard</div>
                                </button>
                                <button type="button" class="col-auto btn rounded-0 lh-1" :class="{ 'text-white bg-2':getMarketPageState() == 'active', 'text-gray':getMarketPageState() == 'locked', 'text-danger':getMarketPageState() == 'forbidden' }" style="width:75px;" @click="showMarketPage()">
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
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <button type="button" class="w-100 btn" :class="{ 'bg-2 text-white':currentFleetsMenu == 'ground' }" @click="currentFleetsMenu = 'ground'">
                                    <span class="h6">Ground Ships</span>
                                </button>
                            </div>
                            <div class="col d-flex flex-column scrollbar" style="overflow-y:auto;">
                                <div v-if="currentFleetsMenu == 'ground'" class="h-100 col p-3">
                                    <FleetsGroundSummary v-for="planet in humanPlanets" :key="planet.id" :planet="planet" />
                                </div>
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <FleetsGroundDetails v-if="currentFleetsGround" :planet="currentFleetsGround" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'map'" class="page">
                        <div class="h-100 d-flex align-items-stretch scrollbar" style="overflow-y:auto;">
                            <div class="h-100 col p-3 position-relative" :class="{ 'bg-perseus':currentNebula.id == 'perseus' }" style="overflow-y:auto;">
                                <MapPlanet v-for="planet in getNebulaPlanets()" :key="planet.id" :planet="planet" />
                            </div>
                        </div>
                    </div>
                    
                    <div v-if="currentPage == 'research'" class="page">
                        <div class="h-100 d-flex align-items-stretch">
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
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
                                                <ResearchSummary v-if="isResearchVisible('intelligence')" :research="researches['intelligence']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('intelligence')" class="text-normal fas fa-fw fa-chevron-circle-left"></i>
                                            </div>
                                            <div class="col">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="isResearchVisible('electronic')" :research="researches['electronic']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('electronic')" class="text-normal fas fa-fw fa-chevron-circle-left"></i>
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
                                                <div v-if="isResearchVisible('karanartofwar')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('karanartofwar')" :research="researches['karanartofwar']" />
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
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col">
                                                <div v-if="isResearchVisible('xiranartofwar')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('xiranartofwar')" :research="researches['xiranartofwar']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('karannuclear')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('karannuclear')" :research="researches['karannuclear']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('spacemining')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('spacemining')" :research="researches['spacemining']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('rhodium')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('rhodium')" :research="researches['rhodium']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('ammonia')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('ammonia')" :research="researches['ammonia']" />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="col-12">
                                        <div class="row g-1 align-items-center">
                                            <div class="col"></div>
                                            <div class="col-auto text-center" style="width:20px;"></div>
                                             <div class="col">
                                                <div v-if="isResearchVisible('protohalean')" class="text-center mb-1" style="height:15px;"><i class="text-normal fas fa-fw fa-chevron-circle-down"></i></div>
                                                <ResearchSummary v-if="isResearchVisible('protohalean')" :research="researches['protohalean']" />
                                            </div>
                                            <div class="col-auto text-center" style="width:20px;">
                                                <div class="text-center mb-1" style="height:15px;"></div>
                                                <i v-if="isResearchVisible('darkmatter')" class="text-normal fas fa-fw fa-chevron-circle-right"></i>
                                            </div>
                                            <div class="col">
                                                <div v-if="isResearchVisible('darkmatter')" class="text-center mb-1" style="height:15px;"></div>
                                                <ResearchSummary v-if="isResearchVisible('darkmatter')" :research="researches['darkmatter']" />
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
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
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

                    <div v-if="currentPage == 'overview'" class="page pt-3 px-2">
                        <div class="h-100 row g-3 scrollbar" style="overflow-y:auto;">
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
                            <div class="col d-flex flex-column scrollbar" style="overflow-y:auto;">
                                <div class="pt-3 px-3">
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
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
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
                            <div class="h-100 col-auto bg-1 border-end p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <div class="row g-3">
                                    <PlanetVignet :planet="currentPlanet" class="col-12" />
                                    <PlanetEnergy :planet="currentPlanet" class="col-12" />
                                    <PlanetResources :planet="currentPlanet" class="col-12" />
                                </div>
                            </div>
                            <div class="h-100 col p-3 scrollbar" style="overflow-y:auto;">
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('extraction')" :key="key" :building="building" />
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
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('production')" :key="key" :building="building" />
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
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('energy')" :key="key" :building="building" />
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
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('research')" :key="key" :building="building" />
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
                                <div v-if="Object.keys(getCurrentPlanetBuildings('other')).length < 1" class="text-center"><span class="text-gray">There is no building to show</span></div>
                                <BuildingSummary v-for="(building, key) of getCurrentPlanetBuildings('other')" :key="key" :building="building" />
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
                                <div v-if="Object.keys(getCurrentPlanetShips()).length < 1" class="text-center"><span class="text-gray">There is no ship to show</span></div>
                                <ShipSummary v-for="(ship, key) of getCurrentPlanetShips()" :key="key" :ship="ship" />
                            </div>
                            <div class="h-100 col-auto bg-1 border-start p-3 scrollbar" style="width:275px; overflow-y:auto;">
                                <ShipDetails v-if="currentShip" :ship="currentShip" />
                            </div>
                        </div>
                    </div>

                    <div v-if="currentPage == 'market'" class="page">
                        <div class="pt-4 text-center">
                            <span class="text-danger">Page not implemented yet</span>
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
var resourcesDef = [
    
    {	id: "ammonia",	reqs: { ammonia: 1 },	},
    {	id: "ammunition",	reqs: { military: 1 },	},
    {	id: "antimatter",	reqs: { quantum: 1 },	},
    {	id: "armor",	reqs: { military: 12 },	},
    {	id: "caesium",	reqs: { karannuclear: 1 },	},
    {	id: "circuit",	reqs: { electronic: 1 },	},
    {	id: "coolant",	reqs: { ice: 10 },	},
    {	id: "darkmatter",	reqs: { darkmatter: 1 },	},
    {	id: "emptybattery",	reqs: { electronic: 8 },	},
    {	id: "engine",	reqs: { military: 16 },	},
    {	id: "fuel",	},
    {	id: "fullbattery",	reqs: { electronic: 8 },	},
    {	id: "graphite",	},
    {	id: "hydrogen",	reqs: { nuclear: 1 },	},
    {	id: "ice",	reqs: { ice: 1 },	},
    {	id: "iron",	},
    {	id: "meissnercell",	reqs: { electronic: 35 },	},
    {	id: "meissnerium",	reqs: { material: 35 },	},
    {	id: "methane",	},
    {	id: "mkembryo",	reqs: { osmium: 1 },	},
    {	id: "nanotube",	reqs: { chemical: 30 },	},
    {	id: "oil",	reqs:{ chemical: 1 },	},
    {	id: "osmium",	reqs: { osmium: 1 },	},
    {	id: "plastic",	reqs:{ material: 8 },	},
    {	id: "qasers",	reqs: { protohalean: 1 },	},
    {	id: "rhodium",	reqs: { hydro: 1 },	},
    {	id: "robot",	reqs: { intelligence: 1 },	},
    {	id: "sand",	reqs:{ mineralogy: 4 },	},
    {	id: "silicon",	reqs:{ electronic: 1 },	},
    {	id: "steel",	},
    {	id: "sulfur",	reqs: { vulcan: 1 },	},
    {	id: "superconductor",	reqs: { electronic: 30 },	},
    {	id: "tammunition",	reqs: { artofwar: 1 },	},
    {	id: "technetium",	reqs: { halean: 1 },	},
    {	id: "thorium",	reqs: { karannuclear: 1 },	},
    {	id: "titanium",	reqs:{ mineralogy: 4 },	},
    {	id: "uammunition",	reqs: { military: 8 },	},
    {	id: "uranium",	reqs:{ mineralogy: 4 },	},
    {	id: "water",	},
]

class PlanetResource {

    constructor(def) {
    
        this.id = def.id
        
        this.prod = 0
        this.count = 0
    }
}

var buildingsDef = [

    {	id: "mine",	type: "extraction",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { iron: 2 },	cost: { iron: 10, steel: 9.75e-4, titanium: 2.1e-12 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.5 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "methaneExtractor",	type: "extraction",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { methane: 1 },	cost: { iron: 100, steel: .1, titanium: 3e-5 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.5 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "graphiteExtractor",	type: "extraction",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic", "radioactive", "acid"],	prod: { graphite: 1 },	cost: { iron: 500, steel: .5, titanium: 4e-4 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.5 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "pumpjack",	type: "extraction",	reqs: { chemical: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { pollution: 1, oil: 1 },	cost: { steel: 1e3, titanium: .9 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "metalCollector",	type: "extraction",	reqs: { mineralogy: 3 },	envs: ["desert", "ice", "terrestrial", "metallic", "radioactive", "acid"],	prod: { titanium: 10, uranium: 1 },	cost: { steel: 2e3, titanium: .9 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -50,	researchPoint: 0,	desc: false,	},
    {	id: "waterPump",	type: "extraction",	reqs: { hydro: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { water: 1 },	cost: { steel: 1e4, titanium: 1e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -10,	researchPoint: 0,	desc: false,	},
    {	id: "sandQuarry",	type: "extraction",	reqs: { mineralogy: 4 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { sand: 1 },	cost: { steel: 5e3, titanium: 1e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -80,	researchPoint: 0,	desc: false,	},
    {	id: "iceDrill",	type: "extraction",	reqs: { ice: 1 },	envs: ["ice"],	prod: { ice: 1 },	cost: { iron: 1e4, steel: 2e3, titanium: 1e3 },	mult: { iron: 1.1, steel: 1.2, titanium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "pumpingPlatform",	type: "extraction",	reqs: null,	envs: ["ocean"],	prod: { water: 5 },	cost: { steel: 5e3, titanium: 1e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "submergedMetalMine",	type: "extraction",	reqs: { hydro: 1 },	envs: ["ocean", "ammonia"],	prod: { iron: 5, titanium: 3, uranium: 1 },	cost: { iron: 5e3, steel: 1e3, titanium: 100 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "submergedSandMine",	type: "extraction",	reqs: { hydro: 1 },	envs: ["ocean", "ammonia"],	prod: { graphite: 2, sand: 1, rhodium: 1, osmium: 1 },	cost: { iron: 5e3, steel: 1e3, titanium: 100 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "rhodiumExtractor",	type: "extraction",	reqs: { rhodium: 1 },	envs: ["desert", "ice", "lava", "metallic", "radioactive", "acid"],	prod: { titanium: 20, rhodium: 2 },	cost: { silicon: 25e4, sand: 1e8, fuel: 1e8 },	mult: { silicon: 1.3, sand: 1.4, fuel: 1.5 },	buildable: true,	energy: -800,	researchPoint: 0,	desc: false,	},
    {	id: "thoriumExtractor",	type: "extraction",	reqs: { karannuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { thorium: 8, caesium: 1 },	cost: { titanium: 5e7, nanotube: 5e5, engine: 3e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: -300,	researchPoint: 0,	desc: false,	},
    {	id: "methaneHarvester",	type: "extraction",	reqs: null,	envs: ["gas"],	prod: { methane: 1 },	cost: { iron: 1e4, steel: 100, plastic: 10, titanium: 2.1e-12 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "hydrogenCondenser",	type: "extraction",	reqs: { nuclear: 1 },	envs: ["gas"],	prod: { hydrogen: 10 },	cost: { steel: 25e3, titanium: 1e4, plastic: 500 },	mult: { steel: 1.2, titanium: 1.3, plastic: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "sandMine",	type: "extraction",	reqs: null,	envs: ["desert"],	prod: { sand: 1 },	cost: { iron: 35e4 },	mult: { iron: 1.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "ammoniaExtractor",	type: "extraction",	reqs: { ammonia: 1 },	envs: ["ammonia"],	prod: { ammonia: 1 },	cost: { nanotube: 1e6, engine: 1e3 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "osmiumExtractor",	type: "extraction",	reqs: { osmium: 1 },	envs: ["ice", "metallic", "radioactive"],	prod: { osmium: 1 },	cost: { silicon: 5e5, rhodium: 3.5e3 },	mult: { silicon: 1.3, rhodium: 1.5 },	buildable: true,	energy: -1200,	researchPoint: 0,	desc: false,	},
    {	id: "sulfurMine",	type: "extraction",	reqs: { vulcan: 1 },	envs: ["lava", "radioactive", "acid"],	prod: { sulfur: 3, graphite: 2 },	cost: { technetium: 1e5, graphite: 1e6 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: false,	},
    {	id: "lavaMine",	type: "extraction",	reqs: { vulcan: 1 },	envs: ["lava", "acid"],	prod: { titanium: 8 },	cost: { technetium: 1e5, graphite: 1e6 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: false,	},
    
    {	id: "darkmatterRefiner",	type: "prod",	reqs: { darkmatter: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { antimatter: -100, meissnercell: -10, darkmatter: 1 },	cost: { qasers: 1e4, meissnerium: 5e5 },	mult: { qasers: 1.2, meissnerium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "ammoniaElectrolyzer",	type: "prod",	reqs: { ammonia: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { ammonia: -10, hydrogen: 30 },	cost: { nanotube: 1e6, engine: 5e3 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: false,	},
    {	id: "methaneProcesser",	type: "prod",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { methane: -2, fuel: 1 },	cost: { iron: 100, steel: .25, titanium: 2e-4 },	mult: { iron: 1.1, steel: 1.2, titanium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "foundry",	type: "prod",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { graphite: -1, iron: -2, fuel: -2, steel: 2 },	cost: { iron: 1e3, steel: .48, titanium: .01 },	mult: { iron: 1.1, steel: 1.2, titanium: 1.3 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "oilRefinery",	type: "prod",	reqs: { chemical: 2 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { oil: -1, fuel: 5 },	cost: { steel: 5e3, titanium: 100 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "plasticFactory",	type: "prod",	reqs: { material: 8 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { oil: -3, plastic: 1 },	cost: { steel: 1e4, titanium: 5e3 },	mult: { steel: 1.25, titanium: 1.35 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: false,	},
    {	id: "sandSmelter",	type: "prod",	reqs: { electronic: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { sand: -1, silicon: 1 },	cost: { steel: 2e4, titanium: 1e4, plastic: 500 },	mult: { steel: 1.1, titanium: 1.2, plastic: 1.3 },	buildable: true,	energy: -50,	researchPoint: 0,	desc: false,	},
    {	id: "electronicFacility",	type: "prod",	reqs: { electronic: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { plastic: -2, silicon: -10, circuit: 1 },	cost: { steel: 1e5, titanium: 5e4, plastic: 2e3 },	mult: { steel: 1.2, titanium: 1.3, plastic: 1.4 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: false,	},
    {	id: "ammunitionFactory",	type: "prod",	reqs: { military: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { steel: -100, fuel: -5, plastic: -3, ammunition: 10 },	cost: { titanium: 1e5, plastic: 2e4 },	mult: { titanium: 1.25, plastic: 1.35 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: true,	},
    {	id: "waterFreezer",	type: "prod",	reqs: { ice: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { water: -2, ice: 3 },	cost: { iron: 28e3, steel: 1e4, titanium: 500 },	mult: { iron: 1.15, steel: 1.27, titanium: 1.35 },	buildable: true,	energy: -30,	researchPoint: 0,	desc: false,	},
    {	id: "nanotubeFactory",	type: "prod",	reqs: { material: 15 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { graphite: -200, plastic: -10, hydrogen: -100, nanotube: 5 },	cost: { titanium: 25e4, plastic: 5e4, circuit: 15e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "coolantFactory",	type: "prod",	reqs: { ice: 10 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { ice: -2e3, methane: -100, coolant: 8 },	cost: { plastic: 3e5, circuit: 1e5 },	mult: { plastic: 1.25, circuit: 1.35 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "robotFactory",	type: "prod",	reqs: { intelligence: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { coolant: -8, circuit: -50, nanotube: -28, steel: -800, fullbattery: -100, robot: 1 },	cost: { plastic: 5e5, circuit: 25e4, nanotube: 5e4 },	mult: { plastic: 1.2, circuit: 1.3, nanotube: 1.4 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "armorFactory",	type: "prod",	reqs: { military: 12 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { steel: -1e3, titanium: -500, plastic: -200, armor: 3 },	cost: { circuit: 5e5, nanotube: 1e5 },	mult: { circuit: 1.25, nanotube: 1.35 },	buildable: true,	energy: -2e3,	researchPoint: 0,	desc: true,	},
    {	id: "engineFactory",	type: "prod",	reqs: { military: 16 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { oil: -500, coolant: -100, circuit: -100, steel: -1e3, titanium: -500, plastic: -200, engine: 2 },	cost: { circuit: 8e5, nanotube: 2e5, robot: 1e3 },	mult: { circuit: 1.2, nanotube: 1.3, robot: 1.4 },	buildable: true,	energy: -2500,	researchPoint: 0,	desc: true,	},
    {	id: "iceMelter",	type: "prod",	reqs: { ice: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "ammonia"],	prod: { fuel: -1, ice: -3, water: 2 },	cost: { iron: 28e3, steel: 1e4, titanium: 500 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "batteryFactory",	type: "prod",	reqs: { electronic: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { hydrogen: -1e3, steel: -35e4, emptybattery: 1e3 },	cost: { titanium: 15e4, plastic: 35e3, circuit: 2e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -100,	researchPoint: 0,	desc: true,	},
    {	id: "electrolyzer",	type: "prod",	reqs: { hydro: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { water: -1, hydrogen: 2 },	cost: { titanium: 25e3, plastic: 1e3, circuit: 100 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -50,	researchPoint: 0,	desc: false,	},
    {	id: "uraniumShellAssembler",	type: "prod",	reqs: { military: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { ammunition: -15, uranium: -8, uammunition: 5 },	cost: { circuit: 3e4, plastic: 5e4 },	mult: { circuit: 1.35, plastic: 1.25 },	buildable: true,	energy: -300,	researchPoint: 0,	desc: true,	},
    {	id: "algaeOilFarm",	type: "prod",	reqs: null,	envs: ["ocean"],	prod: { water: -7, oil: 3 },	cost: { iron: 28e3, steel: 1e4, titanium: 500 },	mult: { iron: 1.15, steel: 1.27, titanium: 1.35 },	buildable: true,	energy: -10,	researchPoint: 0,	desc: false,	},
    {	id: "submergedOilRefinery",	type: "prod",	reqs: null,	envs: ["ocean", "ammonia"],	prod: { oil: -3, fuel: 10 },	cost: { steel: 15e3, titanium: 1e3 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "nanotubeDome",	type: "prod",	reqs: { material: 15 },	envs: ["ocean", "ammonia"],	prod: { graphite: -170, water: -15, plastic: -8, hydrogen: -80, nanotube: 5 },	cost: { titanium: 25e4, plastic: 5e4, circuit: 15e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "tammunitionAssembler",	type: "prod",	reqs: { artofwar: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { uammunition: -3, technetium: -50, steel: -1e5, tammunition: 1 },	cost: { technetium: 1e5, graphite: 2e5 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -750,	researchPoint: 0,	desc: true,	},
    {	id: "particleAccelerator",	type: "prod",	reqs: { quantum: 1 },	envs: ["gas"],	prod: { hydrogen: -1e3, antimatter: 1 },	cost: { steel: 1e8, technetium: 1e6 },	mult: { steel: 1.8, technetium: 1.25 },	buildable: true,	energy: -1e3,	researchPoint: 0,	desc: false,	},
    {	id: "rhodiumSandSmelter",	type: "prod",	reqs: { rhodium: 1 },	envs: ["lava", "acid", "desert", "metallic"],	prod: { rhodium: -5, sand: -150, silicon: 100 },	cost: { silicon: 25e4, sand: 1e8, fuel: 1e8 },	mult: { silicon: 1.3, sand: 1.4, fuel: 1.5 },	buildable: true,	energy: -200,	researchPoint: 0,	desc: false,	},
    {	id: "floatingFuelConverter",	type: "prod",	reqs: null,	envs: ["gas"],	prod: { methane: -5, fuel: 3 },	cost: { steel: 1e4, titanium: 8e3, plastic: 5e3 },	mult: { steel: 1.2, titanium: 1.3, plastic: 1.4 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "polymerizer",	type: "prod",	reqs: { material: 8 },	envs: ["gas"],	prod: { methane: -80, water: -5, plastic: 2 },	cost: { titanium: 2e4, plastic: 1e3 },	mult: { titanium: 1.25, plastic: 1.35 },	buildable: true,	energy: -150,	researchPoint: 0,	desc: false,	},
    {	id: "metallokoptaClonator",	type: "prod",	reqs: { osmium: 1 },	envs: ["ice", "desert", "metallic", "radioactive"],	prod: { osmium: -5, robot: -5, sulfur: -20, silicon: -85, mkembryo: 1 },	cost: { silicon: 25e5, rhodium: 15e3 },	mult: { silicon: 1.3, rhodium: 1.5 },	buildable: true,	energy: -2e3,	researchPoint: 0,	desc: false,	},
    {	id: "ammoniaRefrigerator",	type: "prod",	reqs: { ammonia: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { ammonia: -10, methane: -200, coolant: 8 },	cost: { nanotube: 1e7, engine: 1e6 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "methaneAggregator",	type: "prod",	reqs: { chemical: 30 },	envs: ["lava", "gas"],	prod: { methane: -1e6, nanotube: 20 },	cost: { steel: 1e9, nanotube: 10e6, engine: 1e5 },	mult: { steel: 1.35, nanotube: 1.3, engine: 1.3 },	buildable: true,	energy: -5e3,	researchPoint: 0,	desc: false,	},
    {	id: "ceramicFoundry",	type: "prod",	reqs: { material: 35 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "carbon", "acid"],	prod: { water: -5e5, nanotube: -1e4, rhodium: -2e3, sulfur: -1e3, osmium: -500, iron: -2.5e3, meissnerium: 1 },	cost: { nanotube: 10e6, engine: 1e6 },	mult: { nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    {	id: "superconductorsFactory",	type: "prod",	reqs: { electronic: 30 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "carbon", "acid"],	prod: { meissnerium: -1, coolant: -350, superconductor: 1 },	cost: { nanotube: 10e6, engine: 1e6, meissnerium: 1e3 },	mult: { meissnerium: 1.2, nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -5e3,	researchPoint: 0,	desc: false,	},
    {	id: "cellsFactory",	type: "prod",	reqs: { electronic: 35 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "carbon", "acid"],	prod: { superconductor: -1, circuit: -1e7, nanotube: -1e7, meissnercell: 1 },	cost: { nanotube: 10e6, engine: 1e6, meissnerium: 1e3 },	mult: { meissnerium: 1.2, nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -1e4,	researchPoint: 0,	desc: false,	},
    {	id: "qasersAssembler",	type: "prod",	reqs: { protohalean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { superconductor: -1, circuit: -1e7, nanotube: -1e7, water: -25e4, qasers: 1 },	cost: { nanotube: 1e6, engine: 25e4, meissnerium: 300 },	mult: { meissnerium: 1.2, nanotube: 1.25, engine: 1.35 },	buildable: true,	energy: -1e4,	researchPoint: 0,	desc: false,	},
    {	id: "technetiumFissor",	type: "prod",	reqs: { halean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "radioactive", "ammonia"],	prod: { uranium: -12, technetium: 1 },	cost: { graphite: 5e6, nanotube: 500 },	mult: { graphite: 1.25, nanotube: 1.35 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "haleanAICenter",	type: "prod",	reqs: { halean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "radioactive", "ammonia"],	prod: { circuit: -50, technetium: -5, fullbattery: -100, robot: 1 },	cost: { circuit: 5e5, nanotube: 2e3, technetium: 100 },	mult: { circuit: 1.5, nanotube: 1.3, technetium: 1.2 },	buildable: true,	energy: -500,	researchPoint: 0,	desc: false,	},
    
    {	id: "smallGenerator",	type: "energy",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { fuel: -3 },	cost: { iron: 2e3, steel: 100, titanium: .17 },	mult: { iron: 1.15, steel: 1.27, titanium: 1.35 },	buildable: true,	energy: 20,	researchPoint: 0,	desc: false,	},
    {	id: "thermalPlant",	type: "energy",	reqs: { chemical: 1 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { fuel: -10 },	cost: { iron: 5e3, steel: 1e3, titanium: .17 },	mult: { iron: 2.5, steel: 2.7, titanium: 1.9 },	buildable: true,	energy: 100,	researchPoint: 0,	desc: false,	},
    {	id: "solarCentral",	type: "energy",	reqs: { electronic: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { steel: 1e4, titanium: 1e4, silicon: 1e3 },	mult: { steel: 1.1, titanium: 1.1, silicon: 1.3 },	buildable: true,	energy: 50,	researchPoint: 0,	desc: false,	},
    {	id: "nuclearPowerplant",	type: "energy",	reqs: { nuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { uranium: -10, graphite: -3 },	cost: { titanium: 1e5, plastic: 2e4, graphite: 1e6 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 500,	researchPoint: 0,	desc: false,	},
    {	id: "fusionReactor",	type: "energy",	reqs: { nuclear: 5 },	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: { hydrogen: -150 },	cost: { titanium: 25e4, plastic: 1e5, circuit: 1e4 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: 1100,	researchPoint: 0,	desc: false,	},
    {	id: "batteryPowerPlant",	type: "energy",	reqs: { electronic: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: { fullbattery: -1e4, emptybattery: 9.5e3 },	cost: { titanium: 2e5, plastic: 5e4, circuit: 5e3 },	mult: { titanium: 1.2, plastic: 1.3, circuit: 1.4 },	buildable: true,	energy: 240,	researchPoint: 0,	desc: false,	},
    {	id: "antimatterCollider",	type: "energy",	reqs: { quantum: 1 },	envs: ["gas"],	prod: { antimatter: -1, hydrogen: -1e3 },	cost: { steel: 100e6, technetium: 1e6 },	mult: { steel: 1.8, technetium: 1.25 },	buildable: true,	energy: 8e3,	researchPoint: 0,	desc: false,	},
    {	id: "nuclearPowerplant2",	type: "energy",	reqs: { nuclear: 1 },	envs: ["radioactive"],	prod: { uranium: -10, graphite: -3 },	cost: { titanium: 1e5, plastic: 2e4, graphite: 1e6 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 2e3,	researchPoint: 0,	desc: false,	},
    {	id: "hydrothermalPlant",	type: "energy",	reqs: null,	envs: ["ocean"],	prod: { water: -2, fuel: -10 },	cost: { steel: 1e4, titanium: 2e3 },	mult: { iron: 1.2, steel: 1.35, titanium: 1.5 },	buildable: true,	energy: 100,	researchPoint: 0,	desc: false,	},
    {	id: "bydroelectricPlant",	type: "energy",	reqs: null,	envs: ["ocean"],	prod: { water: -10 },	cost: { steel: 15e3, titanium: 1e4, plastic: 1500 },	mult: { steel: 1.1, titanium: 1.2, plastic: 1.3 },	buildable: true,	energy: 200,	researchPoint: 0,	desc: false,	},
    {	id: "pressurizedWaterReactor",	type: "energy",	reqs: { nuclear: 3 },	envs: ["ocean"],	prod: { uranium: -5, water: -20 },	cost: { titanium: 2e5, plastic: 1e5, graphite: 5e5 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 500,	researchPoint: 0,	desc: false,	},
    {	id: "thoriumReactor",	type: "energy",	reqs: { karannuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { thorium: -5 },	cost: { titanium: 80e6, nanotube: 5e6, engine: 8e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: 2500,	researchPoint: 0,	desc: false,	},
    {	id: "thoriumReactor2",	type: "energy",	reqs: { karannuclear: 1 },	envs: ["radioactive", "ocean"],	prod: { thorium: -5 },	cost: { titanium: 80e6, nanotube: 5e6, engine: 8e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: 5e3,	researchPoint: 0,	desc: false,	},
    {	id: "floatingGenerator",	type: "energy",	reqs: null,	envs: ["gas"],	prod: { pollution: 10, fuel: -3 },	cost: { iron: 2e3, steel: 100, titanium: .17 },	mult: { iron: 1.2, steel: 1.3, titanium: 1.4 },	buildable: true,	energy: 20,	researchPoint: 0,	desc: false,	},
    {	id: "floatingReactor",	type: "energy",	reqs: { nuclear: 5 },	envs: ["gas"],	prod: { hydrogen: -100 },	cost: { plastic: 2e5, circuit: 5e4 },	mult: { plastic: 1.25, circuit: 1.35 },	buildable: true,	energy: 1e3,	researchPoint: 0,	desc: false,	},
    {	id: "pressurizedAmmoniaReactor",	type: "energy",	reqs: { ammonia: 1 },	envs: ["ammonia"],	prod: { thorium: -5, ammonia: -20 },	cost: { titanium: 2e5, plastic: 1e5, graphite: 5e5 },	mult: { titanium: 1.2, plastic: 1.3, graphite: 1.4 },	buildable: true,	energy: 800,	researchPoint: 0,	desc: false,	},
    
    {	id: "ammoniaAirship",	type: "research",	reqs: { ammonia: 1 },	envs: ["gas"],	prod: { ammonia: -100 },	cost: { nanotube: 25e9, coolant: 250e6, hydrogen: 5e9 },	mult: { nanotube: 1.2, coolant: 1.5, hydrogen: 1.3 },	buildable: true,	energy: -15e3,	researchPoint: 1,	desc: false,	},
    {	id: "laboratory",	type: "research",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic"],	prod: null,	cost: { iron: 1e3, steel: 200, titanium: .01 }, 	mult: { iron: 2, steel: 3, titanium: 4 },	buildable: true,	energy: -5,	researchPoint: 4,	desc: false,	},
    {	id: "oceanographicCenter",	type: "research",	reqs: null,	envs: ["ocean", "ammonia"],	prod: { water: -2 },	cost: { steel: 2e4, titanium: 5e3 },	mult: { steel: 1.5, titanium: 2 },	buildable: true,	energy: -50, 	researchPoint: 10,	desc: false,	},
    {	id: "cryogenicLaboratory",	type: "research",	reqs: { ice: 12 },	envs: ["ice", "radioactive"],	prod: { ice: -100 },	cost: { plastic: 3e5, circuit: 1e5 },	mult: { plastic: 1.25, circuit: 1.35 },	buildable: true,	energy: -100, 	researchPoint: 1,	desc: false,	},
    {	id: "karanLaboratory",	type: "research",	reqs: { karannuclear: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "lava", "acid"],	prod: { thorium: -8, caesium: -4, uranium: -25 },	cost: { titanium: 1e8, nanotube: 2e6, engine: 25e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: -1e3,	researchPoint: 8e3,	desc: false,	},
    {	id: "fluidodynamicsCenter",	type: "research",	reqs: null,	envs: ["gas"],	prod: { hydrogen: -5 },	cost: { titanium: 1e4, plastic: 1e3 },	mult: { titanium: 1.5, plastic: 2 },	buildable: true,	energy: -70, 	researchPoint: 10,	desc: false,	},
    {	id: "karanLaboratory2",	type: "research",	reqs: { karannuclear: 1 },	envs: ["radioactive"],	prod: { thorium: -8, caesium: -4, uranium: -25 },	cost: { titanium: 100e6, nanotube: 2e6, engine: 25e4 },	mult: { titanium: 1.2, nanotube: 1.3, engine: 1.2 },	buildable: true,	energy: -1e3,	researchPoint: 16e3,	desc: false,	},
    {	id: "superfluidsCenter",	type: "research",	reqs: { protohalean: 2 },	envs: ["ocean", "ammonia"],	prod: { qasers: -1, superconductors: -10, coolant: -1e3 }, 	cost: { nanotube: 20e6, engine: 3e6, meissnerium: 5e3 },	mult: { meissnerium: 1.3, nanotube: 1.25, engine: 1.3 },	buildable: true,	energy: -38e3,	researchPoint: 25e4,	desc: false,	},
    {	id: "haleanLaboratory",	type: "research",	reqs: { halean: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "radioactive", "ammonia"],	prod: { technetium: -2 },	cost: { circuit: 5e5, nanotube: 2e3, technetium: 100 }, 	mult: { circuit: 1.5, nanotube: 1.3, technetium: 1.2 },	buildable: true,	energy: -500,	researchPoint: 90,	desc: false,	},
    {	id: "vulcanObservatory",	type: "research",	reqs: { vulcan: 1 },	envs: ["lava", "acid"],	prod: { sulfur: -3 },	cost: { technetium: 1e5, graphite: 2e6 },	mult: { technetium: 1.35, graphite: 1.25 },	buildable: true,	energy: -800,	researchPoint: 1,	desc: false,	},
    
    {	id: "shipyard",	type: "other",	reqs: { astronomy: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: null,	cost: { steel: 5e3, titanium: 1e3 },	mult: { steel: 2, titanium: 3.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: false,	},
    {	id: "batteryCharger",	type: "other",	reqs: { electronic: 8 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: { emptybattery: -1e4, fullbattery: 1e4 },	cost: { steel: 5e5, titanium: 1e5, plastic: 2e4 },	mult: { steel: 1.2, titanium: 1.2, plastic: 1.2 },	buildable: true,	energy: -250,	researchPoint: 0,	desc: false,	},
    {	id: "tradeHub",	type: "other",	reqs: { astronomy: 5 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "ammonia"],	prod: null,	cost: { steel: 5e4, titanium: 1e4 },	mult: { steel: 2, titanium: 3.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "wahrianTimeMachine",	type: "other",	reqs: { secret: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { iron: 5e11, steel: 1e12, titanium: 1e10 },	mult: { iron: 1.5, steel: 2, titanium: 2.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateAlpha",	type: "other",	reqs: { secret: 1 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { iron: 5e11, steel: 1e12, titanium: 1e10 },	mult: { iron: 1.5, steel: 2, titanium: 2.2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateBeta",	type: "other",	reqs: { secret: 2 },	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { osmium: 5e7, rhodium: 1e8 },	mult: { rhodium: 2, osmium: 2 },	buildable: true,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateGamma",	type: "other",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { osmium: 5e7, rhodium: 1e8 },	mult: { rhodium: 2, osmium: 2 }, notBuildable: !0,	buildable: false,	energy: 0,	researchPoint: 0,	desc: true,	},
    {	id: "spaceGateDelta",	type: "other",	reqs: null,	envs: ["desert", "ice", "terrestrial", "metallic", "ocean", "gas", "lava", "acid", "radioactive", "ammonia"],	prod: null,	cost: { osmium: 5e7, rhodium: 1e8 },	mult: { rhodium: 2, osmium: 2 }, notBuildable: !0,	buildable: false,	energy: 0,	researchPoint: 0,	desc: true,	},
]

class PlanetBuilding {

    constructor(def) {
    
        this.id = def.id
        this.type = def.type
        this.envs = def.envs
        this.cost = def.cost
        this.prod = def.prod
        this.energy = def.energy
        this.researchPoint = def.researchPoint
        
        this.need = {}
        for (let rId in this.prod) this.need[rId] = false
        
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

    {	id: "vitha",	civIds: ["human"],	type: "colony",	shipyardLevel: 1,	novalue: 1,	reqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .8,	piercing: 0,	storage: 800,	fuel: "fuel",	weight: 2,	combatWeight: 0,	hp: 1,	cost: { iron: 5e4, steel: 8e4, titanium: 5e3 },	desc: false,	},
    {	id: "zb03",	civIds: ["human"],	type: "cargo",	shipyardLevel: 1,	novalue: 1,	reqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .5,	piercing: 0,	storage: 1e4,	fuel: "fuel",	weight: 2200,	combatWeight: 2,	hp: 2,	cost: { steel: 8e4, titanium: 5e3 },	desc: false,	},
    {	id: "zb04",	civIds: ["human"],	type: "cargo",	shipyardLevel: 2,	novalue: 1,	reqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .8,	piercing: 0,	storage: 8e3,	fuel: "fuel",	weight: 2e3,	combatWeight: 2,	hp: 2,	cost: { steel: 8e4, titanium: 1e4 },	desc: false,	},
    {	id: "ark22",	civIds: ["human"],	type: "fighter",	shipyardLevel: 3,	novalue: 0,	reqs: null,	weapon: "laser",	power: 10,	shield: 0,	armor: 100,	speed: 2.8,	piercing: 0,	storage: 100,	fuel: "uranium",	weight: 100,	combatWeight: 0,	hp: 70,	cost: { steel: 2e5, titanium: 1e4, plastic: 200 },	desc: false,	},
    {	id: "ark55",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 4,	novalue: 0,	reqs: null,	weapon: "laser",	power: 6,	shield: 0,	armor: 800,	speed: 2.3,	piercing: 0,	storage: 500,	fuel: "uranium",	weight: 150,	combatWeight: 0,	hp: 180,	cost: { steel: 2e5, titanium: 1e4, plastic: 200 },	desc: false,	},
    {	id: "foxar",	civIds: ["human"],	type: "fighter",	shipyardLevel: 5,	novalue: 0,	reqs: null,	weapon: "laser",	power: 120,	shield: 0,	armor: 2e3,	speed: 1.8,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 550,	combatWeight: 0,	hp: 850,	cost: { titanium: 1e5, plastic: 5e3, circuit: 500 },	desc: false,	},
    {	id: "skydragon",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 6,	novalue: 0,	reqs: null,	weapon: "laser",	power: 80,	shield: 0,	armor: 5e3,	speed: 1.3,	piercing: 0,	storage: 1e4,	fuel: "uranium",	weight: 800,	combatWeight: 0,	hp: 2200,	cost: { titanium: 8e4, plastic: 8e3, circuit: 500 },	desc: false,	},
    {	id: "zb22",	civIds: ["human"],	type: "cargo",	shipyardLevel: 7,	novalue: 1,	reqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 5,	speed: 1.2,	piercing: 0,	storage: 2e5,	fuel: "uranium",	weight: 2500,	combatWeight: 2,	hp: 10,	cost: { titanium: 1e5, plastic: 2e4, circuit: 500 },	desc: false,	},
    {	id: "babayaga",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 8,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 600,	shield: 0,	armor: 8e3,	speed: 1,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1500,	combatWeight: 0,	hp: 5e3,	cost: { plastic: 25e3, circuit: 2e3, ammunition: 550 },	desc: false,	},
    {	id: "zb50",	civIds: ["human"],	type: "cargo",	shipyardLevel: 9,	novalue: 1,	reqs: null,	weapon: "unarmed",	power: 0,	shield: 0,	armor: 10,	speed: .8,	piercing: 0,	storage: 5e6,	fuel: "hydrogen",	weight: 3500,	combatWeight: 35,	hp: 20,	cost: { plastic: 35e3, circuit: 1500 },	desc: false,	},
    {	id: "luxis",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 10,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 500,	shield: 0,	armor: 1e3,	speed: 5.2,	piercing: 25,	storage: 100,	fuel: "hydrogen",	weight: 220,	combatWeight: 0,	hp: 5e3,	cost: { circuit: 12e3, ammunition: 7e3, nanotube: 3e3 },	desc: false,	},
    {	id: "muralla",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 10,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 11,	shield: 8e4,	armor: 5e4,	speed: .15,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 22e3,	combatWeight: 0,	hp: 6e4,	cost: { circuit: 5e3, ammunition: 1e4, nanotube: 12e3 },	desc: false,	},
    {	id: "siber",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 11,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 1e4,	shield: 3e4,	armor: 25e3,	speed: .7,	piercing: 8,	storage: 1e6,	fuel: "hydrogen",	weight: 4e3,	combatWeight: 0,	hp: 15e3,	cost: { ammunition: 7e4, nanotube: 15e3, robots: 150 },	desc: false,	},
    {	id: "alkantara",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 13,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 21e3,	shield: 5e4,	armor: 3e5,	speed: .15,	piercing: 0,	storage: 2e6,	fuel: "hydrogen",	weight: 55e3,	combatWeight: 0,	hp: 6e5,	cost: { ammunition: 25e4, nanotube: 5e4, robots: 1800 },	desc: true,	},
    {	id: "auxilia",	civIds: ["human"],	type: "fighter",	shipyardLevel: 14,	novalue: 0,	reqs: { artofwar: 3 },	weapon: "thermal",	power: 5800,	shield: 0,	armor: 3200,	speed: 1.7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 1340,	combatWeight: 0,	hp: 380,	cost: { technetium: 300, "t-ammunition": 10 },	desc: false,	},
    {	id: "servant",	civIds: ["human"],	type: "fighter",	shipyardLevel: 15,	novalue: 0,	reqs: { osmium: 1 },	weapon: "thermal",	power: 3e4,	shield: 0,	armor: 1e4,	speed: 2.8,	piercing: 0,	storage: 10,	fuel: "rhodium",	weight: 130,	combatWeight: 0,	hp: 2e4,	cost: { silicon: 5e4, rhodium: 1500, "mK Embryo": 10 },	desc: false,	},
    {	id: "orionCargo",	civIds: ["human"],	type: "cargo",	shipyardLevel: 14,	novalue: 1,	reqs: null,	weapon: "laser",	power: 33,	shield: 0,	armor: 20,	speed: 1.5,	piercing: 0,	storage: 250e6,	fuel: "hydrogen",	weight: 5e6,	combatWeight: 2,	hp: 150,	cost: { robots: 200, nanotube: 2e4 },	desc: false,	},
    {	id: "angerPerseus",	civIds: ["human", "rebel"],	type: "fighter",	shipyardLevel: 16,	novalue: 0,	reqs: { quantum: 3 },	weapon: "antimatter",	power: 300e6,	shield: 1e6,	armor: 50e6,	speed: .035,	piercing: 0,	storage: 100e6,	fuel: "hydrogen",	weight: 5e6,	combatWeight: 0,	hp: 5e9,	cost: { iron: 200e9, steel: 2e12, titanium: 50e9, nanotube: 500e6, ammunition: 1e9, "full battery": 350e6, "u-ammunition": 100e6, armor: 10e6, robots: 1e6, engine: 3e5, antimatter: 1e4 },	desc: true,	},
    {	id: "medusaMiner",	civIds: ["human"],	type: "fighter",	shipyardLevel: 18,	novalue: 1,	reqs: { spacemining: 1 },	weapon: "unarmed",	power: 0,	shield: 0,	armor: 1,	speed: .5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 100e6,	combatWeight: 2,	hp: 10,	cost: { iron: 300e6, steel: 1e9, titanium: 100e6, nanotube: 1e6, "full battery": 25e4, robots: 1e5, engine: 500 },	desc: true,	},
    {	id: "andromeda",	civIds: ["human"],	type: "cargo",	shipyardLevel: 17,	novalue: 1,	reqs: { spacemining: 1 },	weapon: "laser",	power: 50,	shield: 0,	armor: 50,	speed: 2,	piercing: 0,	storage: 30e9,	fuel: "hydrogen",	weight: 50e6,	combatWeight: 2,	hp: 1e3,	cost: { robots: 2e4, nanotube: 2e6, engine: 1e3 },	desc: false,	},
    {	id: "munya",	civIds: ["human"],	type: "fighter",	shipyardLevel: 19,	novalue: 0,	reqs: { protohalean: 1 },	weapon: "antimatter",	power: 1e6,	shield: 0,	armor: 2e5,	speed: 15.5,	piercing: 25,	storage: 1e3,	fuel: "hydrogen",	weight: 12e5,	combatWeight: 0,	hp: 2E6,	cost: { ammunition: 1e5, "t-ammunition": 1e5, nanotube: 88e4, robots: 25e3, engine: 15e3, qasers: 20 },	desc: false,	},
    {	id: "soulAndromeda",	civIds: ["human"],	type: "defence",	shipyardLevel: 20,	novalue: 0,	reqs: { darkmatter: 1 },	weapon: "antimatter",	power: 50e9,	shield: 5e6,	armor: 500e6,	speed: .02,	piercing: 0,	storage: 1e9,	fuel: "hydrogen",	weight: 5e9,	combatWeight: 0,	hp: 500e9,	cost: { iron: 5e12, steel: 50e12, titanium: 500e9, nanotube: 30e9, ammunition: 100e9, "full battery": 1e9, "u-ammunition": 10e9, "t-ammunition": 100e6, armor: 1e9, robots: 1e9, engine: 30e6, antimatter: 10e6, meissnerium: 1e6, "dark matter": 1e3 },	desc: true,	},

    {	id: "glassBurson",	civIds: ["council"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 2800,	shield: 0,	armor: 12e3,	speed: .5,	piercing: 0,	storage: 1.2e6,	fuel: "hydrogen",	weight: 15300,	combatWeight: 0,	hp: 1e4,	cost: null,	desc: false,	},
    {	id: "key",	civIds: ["council"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 4e3,	shield: 0,	armor: 18e3,	speed: .3,	piercing: 0,	storage: 5e6,	fuel: "hydrogen",	weight: 13e3,	combatWeight: 0,	hp: 103e3,	cost: null,	desc: false,	},
    {	id: "ark55b",	civIds: ["council", "pirate"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 6,	shield: 0,	armor: 500,	speed: 1.4,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 150,	combatWeight: 0,	hp: 50,	cost: null,	desc: false,	},
    {	id: "arkprp",	civIds: ["council", "pirate"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 26,	shield: 0,	armor: 1500,	speed: 1.4,	piercing: 0,	storage: 100,	fuel: "uranium",	weight: 180,	combatWeight: 0,	hp: 200,	cost: null,	desc: false,	},
    {	id: "union",	civIds: ["council"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 1e15,	shield: 1E7,	armor: 25e9,	speed: 17.25,	piercing: 0,	storage: 8e6,	fuel: "hydrogen",	weight: 5e9,	combatWeight: 0,	hp: 5e14,	cost: null,	desc: false,	},
    {	id: "devil",	civIds: ["council", "andromeda"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 124e3,	shield: 0,	armor: 5e4,	speed: 4,	piercing: 0,	storage: 1e3,	fuel: "rhodium",	weight: 500,	combatWeight: 0,	hp: 25e3,	cost: null,	desc: false,	},
    {	id: "castilla",	civIds: ["council", "andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 80,	shield: 3e4,	armor: 1e6,	speed: .05,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e4,	combatWeight: 0,	hp: 5e6,	cost: null,	desc: false,	},
    {	id: "salantara",	civIds: ["council", "andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 4e12,	shield: 1e4,	armor: 1e9,	speed: 3.12,	piercing: 0,	storage: 10e6,	fuel: "hydrogen",	weight: 1e9,	combatWeight: 0,	hp: 2e12,	cost: null,	desc: false,	},
    {	id: "bellerophon",	civIds: ["council", "andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 25e12,	shield: 5e4,	armor: 8e9,	speed: 5.25,	piercing: 0,	storage: 5e6,	fuel: "hydrogen",	weight: 1.5e9,	combatWeight: 0,	hp: 4e12,	cost: null,	desc: false,	},

    {	id: "mastodon",	civIds: ["phantid"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 22e5,	shield: 0,	armor: 5e5,	speed: .2,	piercing: 0,	storage: 5E7,	fuel: "uranium",	weight: 16e4,	combatWeight: 0,	hp: 5E6,	cost: null,	desc: false,	},
    {	id: "ambjenze",	civIds: ["phantid"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 5e3,	shield: 0,	armor: 3e3,	speed: 1.5,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 1600,	combatWeight: 0,	hp: 5e3,	cost: null,	desc: false,	},
    {	id: "zannsarig",	civIds: ["phantid"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 18e3,	shield: 0,	armor: 9e3,	speed: .4,	piercing: 0,	storage: 500,	fuel: "uranium",	weight: 5e3,	combatWeight: 0,	hp: 22e3,	cost: null,	desc: false,	},

    {	id: "swarm",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 3e4,	shield: 0,	armor: 1e4,	speed: 5.8,	piercing: 0,	storage: 1e3,	fuel: "rhodium",	weight: 100,	combatWeight: 0,	hp: 2e4,	cost: null,	desc: false,	},
    {	id: "enslavedHuman",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 2e6,	shield: 0,	armor: 5e5,	speed: 1.8,	piercing: 0,	storage: 1e4,	fuel: "rhodium",	weight: 1e4,	combatWeight: 0,	hp: 2e6,	cost: null,	desc: false,	},
    {	id: "enslavedQuris",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 3e6,	shield: 0,	armor: 2.5e6,	speed: 2.8,	piercing: 0,	storage: 1e4,	fuel: "rhodium",	weight: 1e3,	combatWeight: 0,	hp: 5e6,	cost: null,	desc: false,	},
    {	id: "enslavedHalean",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 5e6,	shield: 0,	armor: 1e6,	speed: 3.8,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 2500,	combatWeight: 0,	hp: 3e6,	cost: null,	desc: false,	},
    {	id: "heartSwarm",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 2e9,	shield: 0,	armor: 200e6,	speed: .8,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e5,	combatWeight: 0,	hp: 1.6e9,	cost: null,	desc: false,	},
    {	id: "aureaPina",	civIds: ["metallokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 20e9,	shield: 0,	armor: 10e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e6,	combatWeight: 0,	hp: 15e9,	cost: null,	desc: false,	},

    {	id: "haleanSpear",	civIds: ["halean"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 1e4,	shield: 0,	armor: 2e3,	speed: 4.8,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 800,	combatWeight: 0,	hp: 2e4,	cost: null,	desc: false,	},
    {	id: "haleanCounselor",	civIds: ["halean"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 9e4,	shield: 0,	armor: 8e3,	speed: 1.2,	piercing: 0,	storage: 2e5,	fuel: "rhodium",	weight: 580,	combatWeight: 0,	hp: 12e4,	cost: null,	desc: false,	},
    {	id: "juiniDaughter",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 15e6,	shield: 0,	armor: 2e6,	speed: 1.2,	piercing: 0,	storage: 24e6,	fuel: "rhodium",	weight: 1800,	combatWeight: 0,	hp: 872e4,	cost: null,	desc: false,	},
    {	id: "azureHuang",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 221e6,	shield: 0,	armor: 5e6,	speed: .5,	piercing: 0,	storage: 24e6,	fuel: "rhodium",	weight: 5e3,	combatWeight: 0,	hp: 872e4,	cost: null,	desc: false,	},
    {	id: "dreamJuini",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 50e6,	shield: 0,	armor: 1e6,	speed: .2,	piercing: 0,	storage: 100e9,	fuel: "rhodium",	weight: 1e6,	combatWeight: 0,	hp: 1e9,	cost: null,	desc: false,	},
    {	id: "siren",	civIds: ["halean", "juini", "juinika"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 1E6,	shield: 0,	armor: 27e3,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1e3,	combatWeight: 0,	hp: 8E6,	cost: null,	desc: false,	},

    {	id: "auxilia",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 5800,	shield: 0,	armor: 3200,	speed: 2.7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 1340,	combatWeight: 0,	hp: 380,	cost: null,	desc: false,	},
    {	id: "augustus",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 15e6,	shield: 0,	armor: 1.2e6,	speed: .7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 6e3,	combatWeight: 0,	hp: 100e6,	cost: null,	desc: false,	},
    {	id: "leonidas",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 70e6,	shield: 0,	armor: 4e6,	speed: .7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 8e3,	combatWeight: 0,	hp: 200e6,	cost: null,	desc: false,	},
    {	id: "alexander",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 180e6,	shield: 0,	armor: 15e6,	speed: .7,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 12e3,	combatWeight: 0,	hp: 800e6,	cost: null,	desc: false,	},
    {	id: "cerberus",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 200e6,	shield: 0,	armor: 20e6,	speed: .4,	piercing: 0,	storage: 100,	fuel: "hydrogen",	weight: 18e3,	combatWeight: 0,	hp: 400e6,	cost: null,	desc: false,	},
    {	id: "charon",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 300e6,	shield: 0,	armor: 10e6,	speed: .4,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 22800,	combatWeight: 0,	hp: 400e6,	cost: null,	desc: false,	},
    {	id: "lucifer",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 1.3e9,	shield: 0,	armor: 50e6,	speed: .2,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3e4,	combatWeight: 0,	hp: 5e9,	cost: null,	desc: false,	},
    {	id: "deadSoul",	civIds: ["quris"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "technetium",	power: 10e6,	shield: 0,	armor: 8e4,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1800,	combatWeight: 0,	hp: 35e6,	cost: null,	desc: false,	},

    {	id: "natsumiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 3E6,	shield: 0,	armor: 2e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	combatWeight: 0,	hp: 2E6,	cost: null,	desc: false,	},
    {	id: "harumiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 2E6,	shield: 0,	armor: 5e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	combatWeight: 0,	hp: 5E6,	cost: null,	desc: false,	},
    {	id: "akimiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 2E6,	shield: 0,	armor: 5e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	combatWeight: 0,	hp: 5E6,	cost: null,	desc: false,	},
    {	id: "fuyumiko",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 1E6,	shield: 0,	armor: 8e5,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 3800,	combatWeight: 0,	hp: 8E6,	cost: null,	desc: false,	},
    {	id: "zero",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 1800,	shield: 0,	armor: 200,	speed: 3.5,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 300,	combatWeight: 0,	hp: 500,	cost: null,	desc: false,	},
    {	id: "reppu",	civIds: ["robot"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 4e3,	shield: 0,	armor: 800,	speed: 1.8,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 700,	combatWeight: 0,	hp: 2200,	cost: null,	desc: false,	},

    {	id: "blackStar",	civIds: ["pirate"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 57E6,	shield: 0,	armor: 8e5,	speed: .1,	piercing: 0,	storage: 30e6,	fuel: "hydrogen",	weight: 8e5,	combatWeight: 0,	hp: 5E8,	cost: null,	desc: false,	},
    {	id: "noName",	civIds: ["pirate"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 180,	shield: 0,	armor: 2900,	speed: 1.5,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 500,	combatWeight: 0,	hp: 200,	cost: null,	desc: false,	},
    {	id: "angelEyes",	civIds: ["pirate"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 260,	shield: 0,	armor: 5e3,	speed: 1.5,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 500,	combatWeight: 0,	hp: 200,	cost: null,	desc: false,	},
    {	id: "tucoRamirez",	civIds: ["pirate"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 150,	shield: 0,	armor: 1500,	speed: 1.8,	piercing: 0,	storage: 5e3,	fuel: "uranium",	weight: 350,	combatWeight: 0,	hp: 200,	cost: null,	desc: false,	},

    {	id: "marduk",	civIds: ["orion"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 100e6,	shield: 0,	armor: 500e6,	speed: .1,	piercing: 0,	storage: 80,	fuel: "uranium",	weight: 28E6,	combatWeight: 0,	hp: 2e12,	cost: null,	desc: false,	},
    {	id: "aurora",	civIds: ["orion"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 15e4,	shield: 0,	armor: 25e3,	speed: .1,	piercing: 0,	storage: 5E7,	fuel: "uranium",	weight: 8200,	combatWeight: 0,	hp: 8e5,	cost: null,	desc: false,	},
    {	id: "glissard",	civIds: ["orion"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 18e3,	shield: 0,	armor: 5800,	speed: 1.2,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 7e3,	combatWeight: 0,	hp: 6e3,	cost: null,	desc: false,	},
    {	id: "nabonidus",	civIds: ["orion"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 12e3,	shield: 0,	armor: 9200,	speed: .8,	piercing: 0,	storage: 50,	fuel: "uranium",	weight: 8e3,	combatWeight: 0,	hp: 1e4,	cost: null,	desc: false,	},

    {	id: "dieSchoene",	civIds: ["traum"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 3e5,	shield: 0,	armor: 12e4,	speed: .4,	piercing: 0,	storage: 5E7,	fuel: "hydrogen",	weight: 55e3,	combatWeight: 0,	hp: 12e5,	cost: null,	desc: false,	},
    {	id: "alptraum",	civIds: ["traum"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 6600,	shield: 0,	armor: 600,	speed: .9,	piercing: 0,	storage: 1.5e6,	fuel: "hydrogen",	weight: 5600,	combatWeight: 0,	hp: 9e3,	cost: null,	desc: false,	},
    {	id: "engel",	civIds: ["traum"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 600,	shield: 0,	armor: 150,	speed: 1.7,	piercing: 0,	storage: 1e5,	fuel: "hydrogen",	weight: 1800,	combatWeight: 0,	hp: 3e3,	cost: null,	desc: false,	},

    {	id: "cherub",	civIds: ["wahrian"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 12e4,	shield: 3e3,	armor: 1e4,	speed: 1.8,	piercing: 0,	storage: 1e6,	fuel: "rhodium",	weight: 3980,	combatWeight: 0,	hp: 1.8e6,	cost: null,	desc: false,	},
    {	id: "seraph",	civIds: ["wahrian"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 6e6,	shield: 5e3,	armor: 5e4,	speed: .7,	piercing: 0,	storage: 1e6,	fuel: "rhodium",	weight: 11e3,	combatWeight: 0,	hp: 20e6,	cost: null,	desc: false,	},
    {	id: "jericho",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 2e12,	shield: 1e4,	armor: 50e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	combatWeight: 0,	hp: 200e9,	cost: null,	desc: false,	},
    {	id: "sodom",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 1e12,	shield: 1e4,	armor: 20e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	combatWeight: 0,	hp: 130e9,	cost: null,	desc: false,	},
    {	id: "gomorrah",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 1e12,	shield: 1e4,	armor: 20e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	combatWeight: 0,	hp: 130e9,	cost: null,	desc: false,	},
    {	id: "zion",	civIds: ["wahrian"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 500e9,	shield: 1e4,	armor: 10e9,	speed: .1,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 1e9,	combatWeight: 0,	hp: 50e9,	cost: null,	desc: false,	},

    {	id: "aster",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 8e12,	shield: 1e4,	armor: 1e9,	speed: 2.2,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 100e6,	combatWeight: 0,	hp: 1e12,	cost: null,	desc: false,	},
    {	id: "azalea",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 21e12,	shield: 1e4,	armor: 2e9,	speed: 3.3,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 200e6,	combatWeight: 0,	hp: 1200e9,	cost: null,	desc: false,	},
    {	id: "dahlia",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 56e12,	shield: 1e4,	armor: 3e9,	speed: 4.4,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 300e6,	combatWeight: 0,	hp: 1500e9,	cost: null,	desc: false,	},
    {	id: "freesia",	civIds: ["juini", "juinika"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 124e12,	shield: 1e4,	armor: 4e9,	speed: 5.5,	piercing: 0,	storage: 10e6,	fuel: "rhodium",	weight: 500e6,	combatWeight: 0,	hp: 1700e9,	cost: null,	desc: false,	},

    {	id: "wingsPegasus",	civIds: ["andromeda", "arcadia"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 1e14,	shield: 1e5,	armor: 25e9,	speed: 7.25,	piercing: 0,	storage: 8e6,	fuel: "hydrogen",	weight: 5e9,	combatWeight: 0,	hp: 15e12,	cost: null,	desc: false,	},

    {	id: "nux",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 2e5,	shield: 0,	armor: 1e5,	speed: 2,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 3800,	combatWeight: 0,	hp: 1e6,	cost: null,	desc: false,	},
    {	id: "max",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 1e6,	shield: 0,	armor: 15e4,	speed: 1.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 8e3,	combatWeight: 0,	hp: 5e5,	cost: null,	desc: false,	},
    {	id: "furiosa",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 1e6,	shield: 0,	armor: 1e4,	speed: 5.5,	piercing: 5,	storage: 0,	fuel: "hydrogen",	weight: 500,	combatWeight: 0,	hp: 8e3,	cost: null,	desc: false,	},
    {	id: "angharad",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 1e12,	shield: 0,	armor: 1e9,	speed: 1.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e9,	combatWeight: 0,	hp: 1e11,	cost: null,	desc: false,	},
    {	id: "ace",	civIds: ["karan"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 28e14,	shield: 0,	armor: 1e12,	speed: 3.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	combatWeight: 0,	hp: 62e11,	cost: null,	desc: false,	},

    {	id: "sean",	civIds: ["juinika"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 1e16,	shield: 0,	armor: 1e12,	speed: 15.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	combatWeight: 0,	hp: 5e16,	cost: null,	desc: false,	},
    {	id: "dion",	civIds: ["juinika"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 5e16,	shield: 0,	armor: 100e12,	speed: 5.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	combatWeight: 0,	hp: 8e16,	cost: null,	desc: false,	},
    {	id: "gradh",	civIds: ["juinika"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "antimatter",	power: 3e16,	shield: 0,	armor: 1e12,	speed: 8.5,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	combatWeight: 0,	hp: 15e16,	cost: null,	desc: false,	},

    {	id: "alecto",	civIds: ["arcadia"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 100e12,	shield: 0,	armor: 1e12,	speed: 6,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	combatWeight: 0,	hp: 15e14,	cost: null,	desc: false,	},
    {	id: "maegera",	civIds: ["arcadia"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 500e12,	shield: 0,	armor: 1e12,	speed: 6,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	combatWeight: 0,	hp: 35e14,	cost: null,	desc: false,	},
    {	id: "tisiphon",	civIds: ["arcadia"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "ballistic",	power: 8e15,	shield: 0,	armor: 1e12,	speed: 6,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	combatWeight: 0,	hp: 55e15,	cost: null,	desc: false,	},

    {	id: "lightMexager",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 1e15,	shield: 0,	armor: 1e12,	speed: 51,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	combatWeight: 0,	hp: 8e15,	cost: null,	desc: false,	},
    {	id: "lightCompanion",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 12e15,	shield: 0,	armor: 1e12,	speed: 34,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	combatWeight: 0,	hp: 26e15,	cost: null,	desc: false,	},
    {	id: "vela",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 12e16,	shield: 0,	armor: 1e15,	speed: 12,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 5e12,	combatWeight: 0,	hp: 7e17,	cost: null,	desc: false,	},
    {	id: "yola",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 54e17,	shield: 0,	armor: 1e18,	speed: 8,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 50e12,	combatWeight: 0,	hp: 11e18,	cost: null,	desc: false,	},
    {	id: "thunder",	civIds: ["yolur"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "laser",	power: 1e12,	shield: 0,	armor: 1e6,	speed: 32,	piercing: 11,	storage: 0,	fuel: "hydrogen",	weight: 1e9,	combatWeight: 0,	hp: 8e12,	cost: null,	desc: false,	},

    {	id: "darklordNest",	civIds: ["darkarmy"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 1e30,	shield: 18e14,	armor: 1e18,	speed: 105,	piercing: 66,	storage: 0,	fuel: "darkmatter",	weight: 1e14,	combatWeight: 0,	hp: 1e39,	cost: null,	desc: false,	},
    {	id: "munch",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 1e24,	shield: 500e12,	armor: 1e15,	speed: 99,	piercing: 55,	storage: 0,	fuel: "darkmatter",	weight: 1e12,	combatWeight: 0,	hp: 33e24,	cost: null,	desc: false,	},
    {	id: "goya",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 1e21,	shield: 10e12,	armor: 1e12,	speed: 77,	piercing: 44,	storage: 0,	fuel: "darkmatter",	weight: 350e9,	combatWeight: 0,	hp: 15e21,	cost: null,	desc: false,	},
    {	id: "goghVan",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 1e18,	shield: 500e6,	armor: 1e9,	speed: 55,	piercing: 33,	storage: 0,	fuel: "darkmatter",	weight: 180e9,	combatWeight: 0,	hp: 8e18,	cost: null,	desc: false,	},
    {	id: "matisse",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 1e15,	shield: 80e6,	armor: 1e6,	speed: 33,	piercing: 22,	storage: 0,	fuel: "darkmatter",	weight: 50e9,	combatWeight: 0,	hp: 5e15,	cost: null,	desc: false,	},
    {	id: "kingNoir",	civIds: ["darkarmy"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 10e12,	shield: 10e6,	armor: 1e6,	speed: 66,	piercing: 77,	storage: 0,	fuel: "darkmatter",	weight: 1e9,	combatWeight: 0,	hp: 5e12,	cost: null,	desc: false,	},

    {	id: "argentumSpina",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 5e19,	shield: 25e7,	armor: 1e19,	speed: 14,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 100e12,	combatWeight: 0,	hp: 1e20,	cost: null,	desc: false,	},
    {	id: "silverCarapax",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 1e17,	shield: 11e7,	armor: 1e24,	speed: 2,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 10e12,	combatWeight: 0,	hp: 5e18,	cost: null,	desc: false,	},
    {	id: "silverCarrier",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 21e17,	shield: 10e6,	armor: 80e12,	speed: 9,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	combatWeight: 0,	hp: 3e17,	cost: null,	desc: false,	},
    {	id: "silverServant",	civIds: ["protokopta"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "thermal",	power: 3e17,	shield: 1e6,	armor: 1e12,	speed: 28,	piercing: 0,	storage: 0,	fuel: "hydrogen",	weight: 1e12,	combatWeight: 0,	hp: 5e16,	cost: null,	desc: false,	},

    {	id: "lifeSpark",	civIds: ["wardens"],	type: "defence",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 8e30,	shield: 1800e12,	armor: 1e18,	speed: 133,	piercing: 99,	storage: 0,	fuel: "darkmatter",	weight: 100e12,	combatWeight: 0,	hp: 180e33,	cost: null,	desc: false,	},
    {	id: "salvador",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 12e24,	shield: 500e12,	armor: 1e15,	speed: 119,	piercing: 88,	storage: 0,	fuel: "darkmatter",	weight: 1e12,	combatWeight: 0,	hp: 16e24,	cost: null,	desc: false,	},
    {	id: "ernst",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 7e21,	shield: 10e12,	armor: 1e12,	speed: 97,	piercing: 77,	storage: 0,	fuel: "darkmatter",	weight: 350e9,	combatWeight: 0,	hp: 9e21,	cost: null,	desc: false,	},
    {	id: "magrit",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 4e18,	shield: 500e6,	armor: 1e9,	speed: 75,	piercing: 66,	storage: 0,	fuel: "darkmatter",	weight: 180e9,	combatWeight: 0,	hp: 4e18,	cost: null,	desc: false,	},
    {	id: "miro",	civIds: ["wardens"],	type: "fighter",	shipyardLevel: null,	novalue: 0,	reqs: null,	weapon: "darkmatter",	power: 3e15,	shield: 80e6,	armor: 1e6,	speed: 63,	piercing: 55,	storage: 0,	fuel: "darkmatter",	weight: 50e9,	combatWeight: 0,	hp: 2e15,	cost: null,	desc: false,	},
]

class PlanetShip {

    constructor(def) {
    
        this.id = def.id
        this.hp = def.hp
        this.desc = def.desc
        this.type = def.type
        this.armor = def.armor
        this.power = def.power || 0
        this.cost = def.cost
        this.speed = def.speed
        this.shield = def.shield || 0
        this.civIds = def.civIds
        this.weight = def.weight
        this.weapon = def.weapon || "unarmed"
        this.techReq = def.techReq
        this.storage = def.storage
        this.piercing = def.piercing || 0
        this.combatWeight = def.combatWeight || 0
        this.shipyardLevel = def.shipyardLevel
        
        this.count = 0
    }
}

var planetsDef = [

    {	id: "promision",	nebula: "perseus",	civId: "human",	x: 64,	y: 64,	type: "terrestrial",	unlock: null,	influence: 1,	radius: 6833,	temp: 22,	atmos: "oxygen",	orbit: 1,	prod:{ iron: 1, graphite: 1, titanium: 1, silicon: 1, uranium: 1, methane: 1, sand: .5 },	},
    {	id: "vasilis",	nebula: "perseus",	civId: null,	x: 161,	y: 94,	type: "ice",	unlock: "ice",	influence: 1,	radius: 5201,	temp: -21,	atmos: "oxygen",	orbit: 2.9,	prod:{ coolant: 2, ice: 2, methane: 2, titanium: 1.5, iron: 1, uranium: 1, oil: .5 },	},
    {	id: "aequoreas",	nebula: "perseus",	civId: null,	x: 30,	y: 145,	type: "ocean",	unlock: "hydro",	influence: 1,	radius: 8890,	temp: 18,	atmos: "oxygen",	orbit: .7,	prod:{ sand: 2, iron: 1, titanium: 1.5, oil: 1, water: 5 },	},
    {	id: "orpheus",	nebula: "perseus",	civId: null,	x: 53,	y: 229,	type: "gas",	unlock: null,	influence: 1,	radius: 18540,	temp: -141,	atmos: "hydrogen",	orbit: 8.2,	prod:{ hydrogen: 8, methane: 3, technetium: 2 },	},
    {	id: "antirion",	nebula: "perseus",	civId: "pirates",	x: 30,	y: 370,	type: "metallic",	unlock: "military",	influence: 5,	radius: 2230,	temp: -66,	atmos: "methane",	orbit: 5.45,	prod:{ thorium: .2, rhodium: 1.5, iron: 3, titanium: 3, uranium: 2, graphite: .5, methane: 2.4, plastic: 2 },	},
    {	id: "city",	nebula: "perseus",	civId: "city",	x: 166,	y: 302,	type: "ice",	unlock: null,	influence: 8,	radius: 4235,	temp: -8,	atmos: "oxygen",	orbit: 1.82,	prod:{ thorium: .5, ammunition: 2, "u-ammunition": 1.5, iron: 3, graphite: 3, titanium: 2, uranium: 1.5, ice: 1.2, methane: 2.2, osmium: .5 },	},
    {	id: "ishtar",	nebula: "perseus",	civId: "orion",	x: 256,	y: 192,	type: "ocean",	unlock: null,	influence: 12,	radius: 7020,	temp: 3,	atmos: "co2",	orbit: 2.8,	prod:{ graphite: 2, sand: 1, uranium: 1, water: 3.2, oil: .5, nanotube: 2 },	},
    {	id: "traumland",	nebula: "perseus",	civId: "phantids",	x: 448,	y: 236,	type: "terrestrial",	unlock: "environment",	influence: 15,	radius: 6550,	temp: 28,	atmos: "oxygen",	orbit: .7,	prod:{ iron: 1, titanium: 1.2, sand: 1, oil: 2, uranium: .25, water: .6, silicon: 2, graphite: 2, methane: 1 },	},
    {	id: "tataridu",	nebula: "perseus",	civId: "phantids",	x: 384,	y: 64,	type: "terrestrial",	unlock: null,	influence: 18,	radius: 3118,	temp: 38,	atmos: "oxygen",	orbit: .45,	prod:{ iron: .5, uranium: 2, oil: 2.6, water: .25, methane: 2.8, circuit: 2 },	},
    {	id: "zelera",	nebula: "perseus",	civId: "robot",	x: 750,	y: 200,	type: "ice",	unlock: "intelligence",	influence: 22,	radius: 8230,	temp: -30,	atmos: "methane",	orbit: 7.45,	prod:{ osmium: 1.8, ice: 3, iron: 4, titanium: 3.5, uranium: 2.2, robot: 2, methane: 2 },	},
    {	id: "posirion",	nebula: "perseus",	civId: "halean",	x: 854,	y: 62,	type: "gas",	unlock: "halean",	influence: 27,	radius: 48270,	temp: -189,	atmos: "hydrogen",	orbit: 17.22,	prod:{ hydrogen: 51.3, methane: 18.41, technetium: 2 },	},
    {	id: "tsartasis",	nebula: "perseus",	civId: "metallokopta",	x: 680,	y: 750,	type: "desert",	unlock: "rhodium",	influence: 29,	radius: 4504,	temp: 102,	atmos: "co2",	orbit: .31,	prod:{ iron: 2.7, titanium: 3.7, uranium: 5.4, sand: 2, rhodium: 1.6 },	},
    {	id: "acanthus",	nebula: "perseus",	civId: "quris",	x: 275,	y: 422,	type: "ice",	unlock: null,	influence: 31,	radius: 5155,	temp: -12,	atmos: "co2",	orbit: 2.4,	prod:{ darkmatter: 2, thorium: 1.5, osmium: 2.2, methane: 3.2, ice: 5, titanium: 2, uranium: 3 },	},
    {	id: "raknar",	nebula: "perseus",	civId: "quris",	x: 164,	y: 440,	type: "ice",	unlock: null,	influence: 32,	radius: 4081,	temp: -31,	atmos: "co2",	orbit: 4.5,	prod:{ thorium: .5, osmium: 1.8, methane: 2, ice: 3, titanium: 3, uranium: 1, iron: 2, graphite: 5 },	},
    {	id: "shin",	nebula: "perseus",	civId: "metallokopta",	x: 640,	y: 900,	type: "ice",	unlock: "osmium",	influence: 32,	radius: 4504,	temp: -85,	atmos: "co2",	orbit: 8.31,	prod:{ iron: 4.1, titanium: 3.5, uranium: 2.2, ice: 3.2, water: 2, osmium: 3.7, mkembryo: 2 },	},
    {	id: "phorun",	nebula: "perseus",	civId: "halean",	x: 1048,	y: 162,	type: "gas",	unlock: null,	influence: 33,	radius: 28270,	temp: -89,	atmos: "hydrogen",	orbit: 7.22,	prod:{ hydrogen: 31.3, methane: 28.41, technetium: 2.8 },	},
    {	id: "antaris",	nebula: "perseus",	civId: "quris",	x: 410,	y: 687,	type: "metallic",	unlock: "artofwar",	influence: 34,	radius: 6052,	temp: 32,	atmos: "co2",	orbit: 1.4,	prod:{ thorium: 1.2, tammunition: 2, methane: 2, titanium: 1, uranium: 2, iron: 3, rhodium: .5 },	},
    {	id: "teleras",	nebula: "perseus",	civId: "quris",	x: 557,	y: 541,	type: "metallic",	unlock: null,	influence: 36,	radius: 10733,	temp: 202,	atmos: "methane",	orbit: .2,	prod:{ methane: 3, titanium: 2, uranium: 3, iron: 1, rhodium: 1 },	},
    {	id: "jabir",	nebula: "perseus",	civId: "quris",	x: 448,	y: 576,	type: "metallic",	unlock: null,	influence: 38,	radius: 8220,	temp: 182,	atmos: "methane",	orbit: .26,	prod:{ caesium: 1, methane: 2, titanium: 4, uranium: 1, iron: 2, rhodium: 1.2 },	},
    {	id: "epsilon",	nebula: "perseus",	civId: "halean",	x: 948,	y: 310,	type: "desert",	unlock: null,	influence: 46,	radius: 12301,	temp: 74,	atmos: "co2",	orbit: .44,	prod:{ sand: 8, iron: 4.82, titanium: 1.35, uranium: .2, rhodium: 2.5, silicon: 2 },	},
    {	id: "zhura",	nebula: "perseus",	civId: "halean",	x: 1109,	y: 454,	type: "gas",	unlock: "quantum",	influence: 57,	radius: 36420,	temp: -135,	atmos: "hydrogen",	orbit: 5.8,	prod:{ darkmatter: 2, hydrogen: 80, methane: 51.5, technetium: 3.5 },	},
    {	id: "kitrion",	nebula: "perseus",	civId: "metallokopta",	x: 700,	y: 430,	type: "desert",	unlock: null,	influence: 57,	radius: 4504,	temp: 77,	atmos: "co2",	orbit: .77,	prod:{ darkmatter: 2, caesium: 3, methane: 3.2, hydrogen: 15.4, antimatter: 2 },	},
    {	id: "ares",	nebula: "perseus",	civId: "metallokopta",	x: 172,	y: 807,	type: "desert",	unlock: null,	influence: 63,	radius: 3402,	temp: 84,	atmos: "co2",	orbit: 1.42,	prod:{ oil: 2.09, iron: 2.81, titanium: 2.2, sand: 8.33, uranium: 1.37, rhodium: 1.8 },	},
    {	id: "kandi",	nebula: "perseus",	civId: "metallokopta",	x: 360,	y: 920,	type: "ice",	unlock: null,	influence: 65,	radius: 4504,	temp: -22,	atmos: "co2",	orbit: 3.31,	prod:{ iron: 1.76, titanium: 1.75, uranium: 1.2, ice: 4.8, osmium: 1.9, rhodium: 2.5 },	},
    {	id: "bharash",	nebula: "perseus",	civId: "halean",	x: 1198,	y: 636,	type: "gas",	unlock: null,	influence: 68,	radius: 45420,	temp: -92,	atmos: "hydrogen",	orbit: 3.01,	prod:{ hydrogen: 89, methane: 48.5, technetium: 5 },	},
    {	id: "caerul",	nebula: "perseus",	civId: "halean",	x: 1127,	y: 256,	type: "gas",	unlock: null,	influence: 75,	radius: 21155,	temp: -122,	atmos: "hydrogen",	orbit: 9.84,	prod:{ antimatter: 2, hydrogen: 8.66, methane: 3.22, technetium: 8 },	},
    {	id: "mermorra",	nebula: "perseus",	civId: "metallokopta",	x: 820,	y: 590,	type: "gas",	unlock: null,	influence: 82,	radius: 88706,	temp: 305,	atmos: "hydrogen",	orbit: .31,	prod:{ iron: 3.2, titanium: 2.66, uranium: 1.95, silicon: 7.5, sand: 2.4, rhodium: 2.54, oil: 2.7 },	},
    {	id: "xora2",	nebula: "perseus",	civId: "metallokopta",	x: 832,	y: 930,	type: "metallic",	unlock: null,	influence: 128,	radius: 6577,	temp: -181,	atmos: "co2",	orbit: 9.46,	prod:{ caesium: 2.1, thorium: 1.2, osmium: 3.1, iron: 8.7, titanium: 5.7, uranium: 13.4, rhodium: 2.6 },	},
    {	id: "xora",	nebula: "perseus",	civId: "metallokopta",	x: 960,	y: 960,	type: "metallic",	unlock: "secret",	influence: 173,	radius: 8230,	temp: -58,	atmos: "methane",	orbit: 7.45,	prod:{ thorium: .8, osmium: 5, methane: 2.2, iron: 4.1, titanium: 9.5, uranium: 7.2, silicon: 5.2, rhodium: 8.7 },	},
    {	id: "babilo",	nebula: "perseus",	civId: "orion",	x: 378,	y: 318,	type: "ice",	unlock: null,	influence: 650,	radius: 8230,	temp: -58,	atmos: "co2",	orbit: 7.45,	prod:{ nanotube: 5, ice: 3, water: .8, iron: 4, titanium: 3.5, uranium: 2.2, robot: 2, osmium: 3 },	},
    
    {	id: "nassaus",	nebula: "andromeda",	civId: "pirates",	x: 80,	y: 520,	type: "lava",	unlock: "vulcan",	influence: 38,	radius: 6771,	temp: 582,	atmos: "sulfur",	orbit: .1,	prod: { thorium: 1.5, rhodium: 1.8, graphite: 3.4, sulfur: 1, titanium: 5 },	},
    {	id: "solidad",	nebula: "andromeda",	civId: null,	x: 578,	y: 33,	type: "terrestrial",	unlock: null,	influence: 5,	radius: 5741,	temp: 38,	atmos: "oxygen",	orbit: .85,	prod: { iron: 3, graphite: 2, titanium: 3, methane: 2, oil: 1.5, water: .5 },	},
    {	id: "conquest",	nebula: "andromeda",	civId: "wahrian",	x: 655,	y: 158,	type: "ice",	unlock: null,	influence: 900,	radius: 3220,	temp: -158,	atmos: "co2",	orbit: 3,	prod: { coolant: 1.5, ice: 5, titanium: 1, osmium: 2.8, uranium: 1.5, graphite: 1.6 },	},
    {	id: "kartarid",	nebula: "andromeda",	civId: "wahrian",	x: 580,	y: 241,	type: "ice",	unlock: null,	influence: 1850,	radius: 2891,	temp: -171,	atmos: "co2",	orbit: 8,	prod: { coolant: 2, ice: 2.5, titanium: 3.5, uranium: 4, iron: 8, methane: 1.8 },	},
    {	id: "cerberus",	nebula: "andromeda",	civId: "wahrian",	x: 468,	y: 261,	type: "lava",	unlock: null,	influence: 2498,	radius: 4230,	temp: 322,	atmos: "sulfur",	orbit: .45,	prod: { armor: 3, engine: 3, thorium: 4, sulfur: 2.5, graphite: 1.7, titanium: 5, rhodium: .5 },	},
    {	id: "death",	nebula: "andromeda",	civId: "wahrian",	x: 633,	y: 341,	type: "lava",	unlock: null,	influence: 3132,	radius: 5661,	temp: 591,	atmos: "sulfur",	orbit: .25,	prod: { sulfur: 2.7, thorium: 2, rhodium: 3, graphite: 3.5, titanium: 1.2, engine: 5 },	},
    {	id: "yanyin",	nebula: "andromeda",	civId: "juini",	x: 864,	y: 264,	type: "ocean",	unlock: null,	influence: 3844,	radius: 12242,	temp: 31,	atmos: "hydrogen",	orbit: .8,	prod: { sand: 1.5, titanium: .5, oil: 1, water: 3, graphite: 3, rhodium: 11 },	},
    {	id: "siris",	nebula: "andromeda",	civId: "juini",	x: 963,	y: 139,	type: "ocean",	unlock: null,	influence: 5222,	radius: 15180,	temp: 6,	atmos: "hydrogen",	orbit: 1.7,	prod: { sand: 2.5, uranium: .8, oil: 1.2, water: 1.5, graphite: 2.8, osmium: 7.1 },	},
    {	id: "xilea",	nebula: "andromeda",	civId: "juini",	x: 1133,	y: 113,	type: "ocean",	unlock: null,	influence: 6920,	radius: 15705,	temp: 19,	atmos: "hydrogen",	orbit: 1.12,	prod: { caesium: .5, sand: .5, uranium: 3.1, titanium: 2.2, water: 5, graphite: 1 },	},
    {	id: "asun",	nebula: "andromeda",	civId: "juini",	x: 1070,	y: 293,	type: "ocean",	unlock: "protohalean",	influence: 15520,	radius: 13010,	temp: 3,	atmos: "hydrogen",	orbit: 2.04,	prod: { sand: 1, iron: 2, uranium: 5.7, titanium: 2, water: 12 },	},
    {	id: "swamp",	nebula: "andromeda",	civId: "andromeda",	x: 670,	y: 474,	type: "terrestrial",	unlock: "spacemining",	influence: 4711,	radius: 5390,	temp: 21,	atmos: "oxygen",	orbit: .72,	prod: { sand: 1.5, titanium: .5, oil: 1, water: 3, graphite: 3 },	},
    {	id: "columbus",	nebula: "andromeda",	civId: "andromeda",	x: 541,	y: 552,	type: "radioactive",	unlock: null,	influence: 5898,	radius: 8004,	temp: -26,	atmos: "co2",	orbit: 1.5,	prod: { thorium: 1.8, iron: 2, graphite: 5.1, uranium: 15.6, osmium: 2.7 },	},
    {	id: "magellan",	nebula: "andromeda",	civId: "andromeda",	x: 643,	y: 626,	type: "acid",	unlock: null,	influence: 8004,	radius: 4532,	temp: 370,	atmos: "acid",	orbit: .6,	prod: { meissnerium: 1.8, caesium: 1, titanium: 2.5, graphite: 3, sulfur: 5 },	},
    {	id: "gerlache",	nebula: "andromeda",	civId: "andromeda",	x: 809,	y: 592,	type: "ice",	unlock: null,	influence: 11422,	radius: 2801,	temp: -71,	atmos: "oxygen",	orbit: 2.9,	prod: { ice: 2.9, thorium: 3, sand: 1.5, titanium: .5, oil: 1, water: 3, graphite: 3 },	},
    {	id: "gagarin",	nebula: "andromeda",	civId: "andromeda",	x: 834,	y: 713,	type: "terrestrial",	unlock: null,	influence: 21870,	radius: 7268,	temp: 15,	atmos: "oxygen",	orbit: .5,	prod: { darkmatter: 2.2, sand: 1.5, titanium: .5, oil: 1, water: 1, graphite: 3 },	},
    {	id: "alfari",	nebula: "andromeda",	civId: "karan",	x: 320,	y: 84,	type: "desert",	unlock: "karanartofwar",	influence: 580,	radius: 8256,	temp: 68,	atmos: "co2",	orbit: .7,	prod: { ammunition: 2, sand: 5, titanium: 15.5, silid: 3, oil: 3, water: 1, graphite: 8 },	},
    {	id: "xeno",	nebula: "andromeda",	civId: "karan",	x: 200,	y: 51,	type: "radioactive",	unlock: "karannuclear",	influence: 808,	radius: 4009,	temp: -101,	atmos: "co2",	orbit: 11.81,	prod: { uammunition: 2, thorium: 1.8, titanium: 8, graphite: 11, uranium: 7, rhodium: 4.8, osmium: 3.5 },	},
    {	id: "caligo",	nebula: "andromeda",	civId: "karan",	x: 80,	y: 96,	type: "acid",	unlock: null,	influence: 32740,	radius: 6208,	temp: 322,	atmos: "acid",	orbit: .52,	prod: { meissnerium: 1.5, caesium: 1.5, sulfur: 6.2, titanium: 3, graphite: 2, rhodium: 7 },	},
    {	id: "halea",	nebula: "andromeda",	civId: "juinika",	x: 980,	y: 401,	type: "ocean",	unlock: null,	influence: 9e4,	radius: 9889,	temp: 22,	atmos: "hydrogen",	orbit: .8,	prod: { titanium: 18, graphite: 15, sand: 15, water: 18 },	},
    {	id: "persephone",	nebula: "andromeda",	civId: "arcadia",	x: 338,	y: 336,	type: "terrestrial",	unlock: null,	influence: 18e3,	radius: 5366,	temp: 25,	atmos: "oxygen",	orbit: 1.1,	prod: { population: .2, titanium: 18, graphite: 15, sand: 15, water: 18 },	},
    {	id: "hades",	nebula: "andromeda",	civId: "arcadia",	x: 180,	y: 290,	type: "desert",	unlock: null,	influence: 44e3,	radius: 2251,	temp: 41,	atmos: "oxygen",	orbit: .9,	prod: { darkmatter: 1.8, population: .05, titanium: 18, graphite: 15, sand: 15, water: 18 },	},
    {	id: "demeter",	nebula: "andromeda",	civId: "arcadia",	x: 80,	y: 350,	type: "gas",	unlock: null,	influence: 15e4,	radius: 89044,	temp: 422,	atmos: "helium",	orbit: .1,	prod: { darkmatter: 2.5, hydrogen: 6, methane: 54, technetium: 8 },	},
    {	id: "hermr",	nebula: "andromeda",	civId: "yolur",	x: 300,	y: 400,	type: "acid",	unlock: null,	influence: 58e3,	radius: 2007,	temp: 282,	atmos: "co2",	orbit: 4,	prod: { thorium: 2.5, sulfur: 3.4, titanium: 3, engine: 5 },	},
    {	id: "calipsi",	nebula: "andromeda",	civId: "yolur",	x: 210,	y: 500,	type: "ammonia",	unlock: null,	influence: 98e3,	radius: 3015,	temp: -58,	atmos: "nitrogen",	orbit: 5.6,	prod: { ammonia: 6.2, titanium: 2.5, uranium: 1.1, coolant: 5 },	},
    {	id: "auriga",	nebula: "andromeda",	civId: "yolur",	x: 320,	y: 440,	type: "ammonia",	unlock: "ammonia",	influence: 6e4,	radius: 3847,	temp: -64,	atmos: "nitrogen",	orbit: 8.5,	prod: { ammonia: 12, uranium: 5 },	},
    {	id: "cygnus",	nebula: "andromeda",	civId: "yolur",	x: 250,	y: 650,	type: "ammonia",	unlock: null,	influence: 164e3,	radius: 4382,	temp: -72,	atmos: "nitrogen",	orbit: 5.2,	prod: { darkmatter: 2, ammonia: 15.8 },	},
    {	id: "forax",	nebula: "andromeda",	civId: "yolur",	x: 22,	y: 590,	type: "carbon",	unlock: null,	influence: 152e3,	radius: 3766,	temp: -22,	atmos: "nitrogen",	orbit: 3,	prod: { graphite: 25, titanium: 8, thorium: 4.5, nanotube: 55 },	},
    {	id: "volor",	nebula: "andromeda",	civId: "yolur",	x: 230,	y: 820,	type: "carbon",	unlock: null,	influence: 25e4,	radius: 2541,	temp: -28,	atmos: "nitrogen",	orbit: 6.1,	prod: { titanium: 12, graphite: 15, thorium: 7.1, nanotube: 135 },	},
    {	id: "discordia",	nebula: "andromeda",	civId: "darkarmy",	x: 247,	y: 304,	type: "ice",	unlock: null,	influence: 103e3,	radius: 5047,	temp: -20,	atmos: "co2",	orbit: 5.1,	prod: { meissnerium: 2, iron: 7, titanium: .1, methane: .9, water: 1, rhodium: 2.1, uranium: 1.5, ice: 1.7 },	},
    {	id: "mallus",	nebula: "andromeda",	civId: "protokopta",	x: 325,	y: 120,	type: "ammonia",	unlock: null,	influence: 105e3,	radius: 4059,	temp: -62,	atmos: "nitrogen",	orbit: 5.3,	prod: { tammunition: 2, technetium: 4, ammonia: 14.7 },	},
    {	id: "lascura",	nebula: "andromeda",	civId: "wardens",	x: 80,	y: 518,	type: "ocean",	unlock: null,	influence: 23e4,	radius: 8397,	temp: 10,	atmos: "hydrogen",	orbit: 1.5,	prod: { caesium: 5.5, graphite: 6.7, oil: 1.1, water: 7.7, sand: 5.1 },	},
    {	id: "pollux",	nebula: "andromeda",	civId: "darkarmy",	x: 305,	y: 521,	type: "metallic",	unlock: null,	influence: 28e4,	radius: 8720,	temp: -49,	atmos: "methane",	orbit: 2.9,	prod: { iron: 3.7, titanium: 1.7, methane: .7, rhodium: 3.7, uranium: 4.3, thorium: .7, superconductor: 2 },	},
    {	id: "mihandria",	nebula: "andromeda",	civId: "wardens",	x: 60,	y: 814,	type: "terrestrial",	unlock: null,	influence: 213e3,	radius: 3405,	temp: 35,	atmos: "oxygen",	orbit: .6,	prod: { iron: .3, titanium: 11.6, graphite: 3.4, oil: .4, methane: 1.1, water: 6.3, sand: 14.7 },	},
    {	id: "mellivor",	nebula: "andromeda",	civId: "darkarmy",	x: 188,	y: 590,	type: "carbon",	unlock: null,	influence: 5e5,	radius: 2996,	temp: -27,	atmos: "nitrogen",	orbit: 4.6,	prod: { nanotube: 96, titanium: 3.4, graphite: 5.1, thorium: 1.9 },	},
    {	id: "extremandur",	nebula: "andromeda",	civId: "darkarmy",	x: 184, y: 738,	type: "desert",	unlock: null,	influence: 25e4,	radius: 2931,	temp: 81,	atmos: "co2",	orbit: 1.1,	prod: { iron: 3.6, titanium: .9, rhodium: 2, uranium: 4.8, sand: 11.9 },	},
    {	id: "urdum",	nebula: "andromeda",	civId: "wardens",	x: 312,	y: 644,	type: "ammonia",	unlock: null,	influence: 52e4,	radius: 4255,	temp: -72,	atmos: "nitrogen",	orbit: 7.7,	prod: { coolant: 5, ammonia: 13.9 },	},
    {	id: "hordron",	nebula: "andromeda",	civId: "wardens",	x: 365,	y: 824,	type: "gas",	unlock: null,	influence: 75e4,	radius: 88664,	temp: -135,	atmos: "hydrogen",	orbit: 11.2,	prod: { technetium: 15, hydrogen: 15.3, methane: 49.2 },	},
    {	id: "viscarius",	nebula: "andromeda",	civId: "darkarmy",	x: 217,	y: 933,	type: "desert",	unlock: null,	influence: 5e5,	radius: 8641,	temp: 65,	atmos: "co2",	orbit: 1.1,	prod: { titanium: 3.9, graphite: 5.6, rhodium: 2, uranium: 4.4, sand: 4.6 },	},
    {	id: "bolmir",	nebula: "andromeda",	civId: "wardens",	x: 105,	y: 384,	type: "acid",	unlock: null,	influence: 208e3,	radius: 3960,	temp: 285,	atmos: "co2",	orbit: 3.4,	prod: { titanium: .5, sulfur: 1.6, caesium: .4, thorium: .9 },	},
    {	id: "misfir",	nebula: "andromeda",	civId: "wardens",	x: 37,	y: 677,	type: "ice",	unlock: null,	influence: 5e5,	radius: 4262,	temp: -39,	atmos: "co2",	orbit: 7.2,	prod: { meissnercell: 2, iron: 6.8, titanium: 3.1, ice: 3.3, thorium: .4 },	},
    {	id: "vehemir",	nebula: "andromeda",	civId: "darkarmy",	x: 91,	y: 934,	type: "lava",	unlock: null,	influence: 48e4,	radius: 2493,	temp: 183,	atmos: "sulfur",	orbit: 2.5,	prod: { rhodium: 6.6, sulfur: 2.2, thorium: 1 },	},

    {	id: "xirandrus",	nebula: "void",	civId: "protokopta",	x: 100,	y: 250,	type: "terrestrial",	unlock: "xiranartofwar",	influence: 88e3,	radius: 5974,	temp: 21,	atmos: "oxygen",	orbit: .5,	prod: { steel: .1, iron: .8, titanium: 14.3, graphite: 9.4, oil: .8, water: 4.1, sand: .1 },	},
    {	id: "peleuvis",	nebula: "void",	civId: "darkarmy",	x: 404,	y: 980,	type: "ocean",	unlock: null,	influence: 7e5,	radius: 14179,	temp: 2,	atmos: "hydrogen",	orbit: 2.1,	prod: { iron: .6, graphite: 10.9, water: 1.2, sand: 13.1 },	},
    {	id: "cranium",	nebula: "void",	civId: "darkarmy",	x: 418,	y: 469,	type: "lava",	unlock: null,	influence: 46e4,	radius: 4572,	temp: 203,	atmos: "sulfur",	orbit: 4,	prod: { steel: .1, titanium: 4.7, graphite: .2, rhodium: 5.6, ice: 2.3, sulfur: 1, thorium: 3.9 },	},
    {	id: "exabolan",	nebula: "void",	civId: "darkarmy",	x: 510,	y: 885,	type: "radioactive",	unlock: null,	influence: 5e5,	radius: 7301,	temp: -48,	atmos: "co2",	orbit: 7.2,	prod: { steel: .2, graphite: 4.7, osmium: 2.2, rhodium: 1.2, uranium: 14.1, thorium: .2 },	},
    {	id: "madame",	nebula: "void",	civId: "darkarmy",	x: 382,	y: 257,	type: "carbon",	unlock: null,	influence: 3e5,	radius: 2732,	temp: -28,	atmos: "nitrogen",	orbit: 4.4,	prod: { darkmatter: 1.3, titanium: 9, graphite: 7.2, thorium: 1 },	},
    {	id: "elon",	nebula: "void",	civId: "wardens",	x: 689,	y: 800,	type: "ocean",	unlock: null,	influence: 8e5,	radius: 14842,	temp: 20,	atmos: "hydrogen",	orbit: .9,	prod: { nanotube: .5, titanium: 14.8, oil: .8, water: 4.9, sand: 2.8 },	},
    {	id: "gora",	nebula: "void",	civId: "darkarmy",	x: 561,	y: 678,	type: "desert",	unlock: null,	influence: 7e5,	radius: 5171,	temp: 99,	atmos: "co2",	orbit: .5,	prod: { titanium: 1.6, oil: 2.2, water: 5.2, rhodium: 1.9, uranium: 1.8, sand: 11.5 },	},
    {	id: "yllirium",	nebula: "void",	civId: "wardens",	x: 643,	y: 961,	type: "ice",	unlock: null,	influence: 9e5,	radius: 3367,	temp: -27,	atmos: "co2",	orbit: 4.4,	prod: { meissnercell: 2, iron: 4.9, titanium: .4, methane: .6, uranium: 2.9, ice: .3 },	},
    {	id: "malus",	nebula: "void",	civId: "darkarmy",	x: 696,	y: 457,	type: "acid",	unlock: null,	influence: 8e5,	radius: 3932,	temp: 313,	atmos: "co2",	orbit: 2.9,	prod: { titanium: .2, rhodium: 5.2, sulfur: 2.7, caesium: .9 },	},
    {	id: "janus",	nebula: "void",	civId: "wardens",	x: 433,	y: 691,	type: "gas",	unlock: null,	influence: 85e4,	radius: 80462,	temp: -103,	atmos: "hydrogen",	orbit: 7.1,	prod: { technetium: 8, antimatter: 12, hydrogen: 76.5, methane: 37.5, caesium: 2.2 },	},
    {	id: "aquarius",	nebula: "void",	civId: "darkarmy",	x: 487,	y: 143,	type: "ammonia",	unlock: null,	influence: 6e5,	radius: 3446,	temp: -60,	atmos: "nitrogen",	orbit: 7.6,	prod: { uranium: 2.4, ammonia: 12 },	},
    {	id: "japheth",	nebula: "void",	civId: "wardens",	x: 659,	y: 597,	type: "gas",	unlock: null,	influence: 8e5,	radius: 31706,	temp: -98,	atmos: "hydrogen",	orbit: 4.7,	prod: { technetium: 7, antimatter: 8, hydrogen: 42.6, methane: 17.4 },	},
    {	id: "poligon",	nebula: "void",	civId: "wardens",	x: 830,	y: 685,	type: "gas",	unlock: null,	influence: 8e5,	radius: 42666,	temp: -109,	atmos: "hydrogen",	orbit: 3.8,	prod: { technetium: 9, antimatter: 3, hydrogen: 23.5, methane: 31.1 },	},
    {	id: "atlas",	nebula: "void",	civId: "protokopta",	x: 258,	y: 18,	type: "gas",	unlock: null,	influence: 302e3,	radius: 44368,	temp: -141,	atmos: "hydrogen",	orbit: 9.7,	prod: { darkmatter: 1.5, technetium: 16, antimatter: 11, hydrogen: 30.6, methane: 40.7 },	},
    {	id: "augmeris",	nebula: "void",	civId: "protokopta",	x: 140,	y: 69,	type: "radioactive",	unlock: null,	influence: 44e4,	radius: 7666,	temp: -37,	atmos: "co2",	orbit: 9.2,	prod: { steel: .25, iron: .2, graphite: 8.7, osmium: 1.2, uranium: 12.8, thorium: .6 },	},
    {	id: "cetus",	nebula: "void",	civId: "wardens",	x: 492,	y: 344,	type: "ocean",	unlock: null,	influence: 52e4,	radius: 13479,	temp: 3,	atmos: "co2",	orbit: 2.4,	prod: { titanium: 11.4, graphite: .1, oil: .8, water: 4.6, osmium: 5, sand: .6 },	},
    {	id: "erpervestalis",	nebula: "void",	civId: "wardens",	x: 884,	y: 551,	type: "acid",	unlock: null,	influence: 9e5,	radius: 5098,	temp: 284,	atmos: "acid",	orbit: 1.1,	prod: { meissnerium: 3, titanium: .3, graphite: .9, sulfur: 5.9 },	},
    {	id: "etaaras",	nebula: "void",	civId: "wardens",	x: 643,	y: 78,	type: "ammonia",	unlock: null,	influence: 7e5,	radius: 3905,	temp: -66,	atmos: "nitrogen",	orbit: 5.9,	prod: { ammonia: 1.7 },	},
    {	id: "jardin",	nebula: "void",	civId: "wardens",	x: 784,	y: 906,	type: "ammonia",	unlock: null,	influence: 1e6,	radius: 4218,	temp: -62,	atmos: "nitrogen",	orbit: 5.6,	prod: { ammonia: 7.9 },	},
    {	id: "karmirion",	nebula: "void",	civId: "darkarmy",	x: 986,	y: 774,	type: "carbon",	unlock: null,	influence: 9e5,	radius: 3048,	temp: -27,	atmos: "nitrogen",	orbit: 4.3,	prod: { titanium: 6.4, graphite: 12.6, thorium: 3.4 },	},
    {	id: "parai",	nebula: "void",	civId: "wardens",	x: 914,	y: 909,	type: "terrestrial",	unlock: null,	influence: 15e5,	radius: 5679,	temp: 24,	atmos: "oxygen",	orbit: .7,	prod: { iron: .7, titanium: 16.7, graphite: 2.4, oil: .4, water: 12.7, sand: 10.6 },	},
    {	id: "premeza",	nebula: "void",	civId: "wardens",	x: 731,	y: 173,	type: "terrestrial",	unlock: null,	influence: 7e5,	radius: 5468,	temp: 37,	atmos: "oxygen",	orbit: .6,	prod: { steel: .3, titanium: 13.8, graphite: 14.7, oil: 1.5, water: 3.3 },	},
    {	id: "unia",	nebula: "void",	civId: "darkarmy",	x: 955,	y: 352,	type: "radioactive",	unlock: null,	influence: 15e5,	radius: 6536,	temp: -33,	atmos: "co2",	orbit: 5.8,	prod: { iron: .8, titanium: 6.2, graphite: 7.3, osmium: 2.5, rhodium: .3, uranium: 6.6, thorium: .8 },	},
    {	id: "vanubis",	nebula: "void",	civId: "protokopta",	x: 486,	y: 18,	type: "metallic",	unlock: null,	influence: 4e5,	radius: 3405,	temp: 2,	atmos: "co2",	orbit: 4.5,	prod: { steel: .5, iron: 8.4, titanium: .9, methane: .5, rhodium: 6.7, uranium: 6.3, caesium: 1.9, thorium: .9 },	},
    {	id: "vikasuka",	nebula: "void",	civId: "darkarmy",	x: 636,	y: 344,	type: "metallic",	unlock: null,	influence: 7e5,	radius: 10730,	temp: -69,	atmos: "methane",	orbit: 5,	prod: { iron: .2, titanium: 6.7, methane: 1.9, rhodium: 4.5, uranium: 8.4, caesium: .8 },	},
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
        this.prod = def.prod
        
        this.resources = {}
        resourcesDef.forEach(def => { this.resources[def.id] = new PlanetResource(def) })
        
        this.energy = { prod: 0, consum: 0, }
        this.researchPoint = { prod: 0, }
        
        this.buildings = {}
        buildingsDef.forEach(def => { this.buildings[def.id] = new PlanetBuilding(def) })        
        
        this.ships = {}
        shipsDef.forEach(def => { this.ships[def.id] = new PlanetShip(def) })        
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
                        
                        for (let rId in building.prod) {
                            this.resources[rId].prod += building.prod[rId] * building.count * energyCoeff
                            if (this.prod[rId]) this.resources[rId].prod *= this.prod[rId]
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
        
        for (let rId in building.prod) {
            ret[rId] = building.prod[rId] * count
            if (this.prod[rId]) ret[rId] *= this.prod[rId]
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
        for (let rId in building.cost) {
        
            let t1 = Math.pow(building.cost[rId].mult, building.count)
            ret[rId] = building.cost[rId].count * t1
            
            for (let i = 1; i < count; i++) {
            
                t1 *= building.cost[rId].mult
                ret[rId] += building.cost[rId].count * t1
            }
        }
        
        return ret
    }
    
    getBuildingRefund(bId, count) {
    
        let ret = {}
        
        for (let rId in this.buildings[bId].cost) {
            ret[rId] = 0
            for (let n = 0; n < count; n++)
                ret[rId] += (this.buildings[bId].cost[rId].count * Math.pow(this.buildings[bId].cost[rId].mult, this.buildings[bId].count - n - 1))
        }

        for (let rId in ret) ret[rId] /= 2
        
        return ret
    }
    
    canBuy(cost) {
    
        let ret = true
        
        for (let rId in cost)
            if (cost[rId] >= 1 && this.resources[rId].count < cost[rId]) {            
                ret = false
                break
            }
            
        return ret
    }

    canProduce(bId) {
    
        let ret = true
        
        let building = this.buildings[bId]
        
        if (building.type != "extraction") {
            for (let rId in building.prod) {
                if (this.resources[rId].count + (building.count * building.prod[rId]) < 0) {
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
        for (let rId in ship.cost)        
            ret[rId] = ship.cost[rId] * count
        
        return ret
    }
    
    buildShip(sId, count) {
    
        let ship = this.ships[sId]
        for (let rId in ship.cost)        
            this.resources[rId].count -= ship.cost[rId] * count
        
        ship.count += count
    }

    getGroundShipCount() {
    
        let ret = 0
        
        for (let sId in this.ships) {
            let ship = this.ships[sId]
            ret += ship.count
        }
        
        return ret
    }
}

var nebulasDef = [

    {	id: "perseus",	},
    {	id: "andromeda",	},
    {	id: "void",	},
]

class Nebula {

    constructor(def) {
    
        this.id = def.id
        
        this.planets = {}
    }
}

var researchesDef = [

    { id: "astronomy", desc: true, researchPoint: 3e3, mult: 4.3,
      shipBonuses: {
        vitha: [
            { attrib: "speed", minLevel: 1, value: 12 },
        ],
      },
      unlocks: { shipyard: 1, tradehub: 5 },
      isVisible(state) { return true },
    },
    { id: "science", researchPoint: 4e4, mult: 2.5, reqs:{ astronomy: 1 }, max: 64,
      buildingBonuses: {
        laboratory: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        ammoniaAirship: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        oceanographicCenter: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        cryogenicLaboratory: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        karanLaboratory: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        fluidodynamicsCenter: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        karanLaboratory2: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        superfluidsCenter: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        haleanLaboratory: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
        vulcanObservatory: [{ attrib: "researchPoint", minLevel: 1, value: 11 }],
      },
      isVisible(state) { return true },
    },    

    { id: "mineralogy", researchPoint: 125, mult: 2.2,
      buildingBonuses: {
        mine: [{ attrib: "prod", rId:"iron", minLevel: 1, value: 25, maxLevel: 125, reduction: .2 }],
        graphiteExtractor: [{ attrib: "prod", rId:"graphite", minLevel: 1, value: 12 }],
        submergedMetalMine: [
            { attrib: "prod", rId: "iron", minLevel: 1, value: 12 },
            { attrib: "prod", rId: "titanium", minLevel: 1, value: 12 },
            { attrib: "prod", rId: "uranium", minLevel: 1, value: 12 },
        ],
        submergedSandMine: [
            { attrib: "prod", rId:"sand", minLevel: 1, value: 12 },
            { attrib: "prod", rId:"graphite", minLevel: 1, value: 12 },
        ],
        lavaMine: [{ attrib: "prod", rId:"graphite", minLevel: 1, value: 12 }],
        sulfurMine: [{ attrib: "prod", rId:"titanium", minLevel: 1, value: 18 }],
        rhodiumExtractor: [{ attrib: "prod", rId:"titanium", minLevel: 1, value: 18 }],
        metalCollector: [
            { attrib: "prod", rId:"titanium", minLevel: 4, value: 18 },
            { attrib: "prod", rId:"uranium", minLevel: 4, value: 12 },
        ],
        sandQuarry: [{ attrib: "prod", rId:"sand", minLevel: 4, value: 12 }],
        sandMine: [{ attrib: "prod", rId:"sand", minLevel: 4, value: 12 }],
        pumpjack: [{ attrib: "prod", rId:"oil", minLevel: 4, value: 8 }],
        algaeOilFarm: [{ attrib: "prod", rId:"oil", minLevel: 4, value: 8 }],
      },
      unlocks: { metalCollector: 3, sandQuarry: 4 },
      isVisible(state) { return true },
    },
    { id: "material", researchPoint: 500, mult: 2.2, reqs:{ mineralogy: 1 },
      buildingBonuses: {
        foundry: [{ attrib: "prod", rId: "steel", minLevel: 1, value: 50, maxLevel: 100, reduction: .5, }],
        plasticFactory: [{ attrib: "prod", rId: "plastic", minLevel: 8, value: 25 }],
        polymerizer: [{ attrib: "prod", rId: "plastic", minLevel: 8, value: 25 }],
        nanotubeFactory: [{ attrib: "prod", rId: "nanotube", minLevel: 15, value: 12 }],
        nanotubeDome: [{ attrib: "prod", rId: "nanotube", minLevel: 15, value: 12 }],
        ceramicFoundry: [{ attrib: "prod", rId: "meissnerium", minLevel: 36, value: 8 }],
      },
      unlocks: { plasticFactory: 8, nanotubeFactory: 15, nanotubeDome: 15, ceramicFoundry: 35 },
      isVisible(state) { return true },
    },
    { id: "chemical", researchPoint: 1200, mult: 2.2, reqs:{ material: 1 },
      buildingBonuses: {
        methaneProcesser: [{ attrib: "prod", rId: "fuel", minLevel: 1, value: 25, }],
        methaneHarvester: [{ attrib: "prod", rId: "methane", minLevel: 1, value: 20, }],
        oilRefinery: [{ attrib: "prod", rId: "fuel", minLevel: 3, value: 12, }],
        methaneAggregator: [{ attrib: "prod", rId: "fuel", minLevel: 31, value: 20, }],
      },
      unlocks: { pumpjack: 1, thermalPlant: 1, oilRefinery: 2, methaneAggregator: 30 },
      isVisible(state) { return state.isResearchUnlocked('material') },
    },
    { id: "electronic", researchPoint: 5e4, mult: 2.2, reqs:{ material: 5 },
      buildingBonuses: {
        electronicFacility: [
            { attrib: "prod", rId: "circuit", minLevel: 2, value: 30 },
        ],
        superconductorsFactory: [
            { attrib: "prod", rId: "superconductor", minLevel: 31, value: 8 },
        ],
        batteryCharger: [
            { attrib: "cost", rId: "steel", minLevel: 8, value: -50 },
            { attrib: "cost", rId: "titanium", minLevel: 8, value: -50 },
            { attrib: "cost", rId: "plastic", minLevel: 8, value: -50 },
        ],
        batteryPowerPlant: [
            { attrib: "cost", rId: "titanium", minLevel: 8, value: -50 },
            { attrib: "cost", rId: "plastic", minLevel: 8, value: -50 },
            { attrib: "cost", rId: "circuit", minLevel: 8, value: -50 },
        ],
        cellsFactory: [
            { attrib: "prod", rId: "meissnercell", minLevel: 35, value: 12.5, maxLevel: 60, reduction: .5 },
        ],
      },
      unlocks: { sandSmelter: 1, electronicFacility: 1, solarCentral: 1, batteryFactory: 8, batteryPowerPlant: 8, batteryCharger: 8, superconductorsFactory: 30, cellsFactory: 35 },
      isVisible(state) { return state.isResearchUnlocked('material') },
    },
    { id: "ice", researchPoint: 1e4, mult: 2.2, reqs:{ material: 3 },
      buildingBonuses: {
        iceDrill: [{ attrib: "prod", rId: "ice", minLevel: 2, value: 25 }],
        coolantFactory: [{ attrib: "prod", rId: "coolant", minLevel: 10, value: 12 }],
        ammoniaRefrigerator: [{ attrib: "prod", rId: "coolant", minLevel: 10, value: 12 }],
        cryogenicLaboratory: [{ attrib: "researchPoint", minLevel: 12, value: 12 }],
      },
      unlocks: { iceDrill: 1, waterFreezer: 1, iceMelter: 1, coolantFactory: 10, cryogenicLaboratory: 12 },
      isVisible(state) { return state.isResearchUnlocked('material') && state.planets["vasilis"].civId == "human" },
    },
    { id: "military", researchPoint: 33e3, mult: 2.2, reqs:{ astronomy: 4 },
      buildingBonuses: {
        ammunitionFactory: [{ attrib: "prod", rId: "ammunition", minLevel: 2, value: 12 }],
        uraniumShellAssembler: [{ attrib: "prod", rId: "uammunition", minLevel: 9, value: 12 }],
        armorFactory: [{ attrib: "prod", rId: "armor", minLevel: 13, value: 12 }],
        engineFactory: [{ attrib: "prod", rId: "engine", minLevel: 17, value: 12 }],
      },
      unlocks: { ammunitionFactory: 1, uraniumShellAssembler: 8, armorFactory: 12, engineFactory: 16 },
      isVisible(state) { return state.isResearchUnlocked('astronomy') && state.planets["antirion"].civId == "human" },
    },
    { id: "nuclear", researchPoint: 5e5, mult: 2.2, reqs:{ hydro: 3 },
      buildingBonuses: {
        fusionReactor: [{ attrib: "prod", rId: "hydrogen", minLevel: 2, value: 9.5 }],
      },
      unlocks: { hydrogenCondenser: 1, nuclearPowerplant: 1, nuclearPowerplant2: 1, pressurizedWaterReactor: 3, fusionReactor: 5, floatingReactor: 5 },
      isVisible(state) { return state.isResearchUnlocked('hydro') },
    },
    { id: "halean", researchPoint: 1e8, mult: 2.2, reqs:{ intelligence: 1 },
      buildingBonuses: {
        technetiumFissor: [{ attrib: "prod", rId: "technetium", minLevel: 1, value: 12, }],
        haleanLaboratory: [{ attrib: "researchPoint", minLevel: 1, value: 12, }],
        robotFactory: [{ attrib: "prod", rId: "robot", minLevel: 1, value: 12, }],
      },
      unlocks: { technetiumFissor: 1, haleanAICenter: 1, haleanLaboratory: 1 },
      isVisible(state) { return state.isResearchUnlocked('intelligence') && state.planets["posirion"].civId == "human" },
    },
    { id: "artofwar", researchPoint: 5e8, mult: 1.6, reqs:{ military: 12 },
      shipBonuses: {
        vitha: [{ attrib: "power", minLevel: 1, value: 5, }],
        zb03: [{ attrib: "power", minLevel: 1, value: 5, }],
        zb04: [{ attrib: "power", minLevel: 1, value: 5, }],
        ark22: [{ attrib: "power", minLevel: 1, value: 5, }],
        ark55: [{ attrib: "power", minLevel: 1, value: 5, }],
        foxar: [{ attrib: "power", minLevel: 1, value: 12, }],
        skydragon: [{ attrib: "power", minLevel: 1, value: 12, }],
        zb22: [{ attrib: "power", minLevel: 1, value: 5, }],
        babayaga: [{ attrib: "power", minLevel: 1, value: 15, }],
        zb50: [{ attrib: "power", minLevel: 1, value: 5, }],
        luxis: [{ attrib: "power", minLevel: 1, value: 5, }],
        muralla: [{ attrib: "power", minLevel: 1, value: 5, }],
        siber: [{ attrib: "power",  minLevel: 1, value: 15, }],
        alkantara: [{ attrib: "power", minLevel: 1, value: 5, }],
        auxilia: [{ attrib: "power",  minLevel: 1, value: 15, }],
        servant: [{ attrib: "power", minLevel: 1, value: 5, }],
        orionCargo: [{ attrib: "power", minLevel: 1, value: 5, }],
        angerPerseus: [{ attrib: "power", minLevel: 1, value: 5, }],
        medusaMiner: [{ attrib: "power", minLevel: 1, value: 5, }],
        andromeda: [{ attrib: "power", minLevel: 1, value: 5, }],
        munya: [{ attrib: "power", minLevel: 1, value: 5, }],
        soulAndromeda: [{ attrib: "power", minLevel: 1, value: 5, }],
      },
      unlocks: { tammunitionAssembler: 1, auxilia: 3 },
      isVisible(state) { return state.isResearchUnlocked('military') && state.planets["antaris"].civId == "human" },
    },
    { id: "intelligence", researchPoint: 5e7, mult: 2.2, reqs:{ electronic: 8 },
      buildingBonuses: {
        robotFactory: [{ attrib: "researchPoint", minLevel: 1, value: 12 }],
      },
      unlocks: { robotFactory: 1 },
      isVisible(state) { return state.isResearchUnlocked('electronic') && state.planets["zelera"].civId == "human" },
    },
    { id: "vulcan", researchPoint: 2e8, mult: 2.2, reqs:{ mineralogy: 17 },
      buildingBonuses: {
        sulfurMine: [
            { attrib: "prod", rId: "graphite", minLevel: 1, value: 12 },
            { attrib: "prod", rId: "sulfur", minLevel: 1, value: 12 },
        ],
        lavaMine: [{ attrib: "prod", rId: "titanium", minLevel: 1, value: 12, }],
        vulcanObservatory: [{ attrib: "researchPoint", minLevel: 1, value: 12, }],
      },
      unlocks: { sulfurMine: 1, lavaMine: 1, vulcanObservatory: 1 },
      isVisible(state) { return state.isResearchUnlocked('mineralogy') && state.planets["nassaus"].civId == "human" },
    },  
    { id: "hydro", researchPoint: 2e4, mult: 2.2, reqs:{ chemical: 3 },
      buildingBonuses: {
        waterPump: [{ attrib: "prod", rId: "water", minLevel: 1, value: 12, }] },
        pumpingPlatform: [{ attrib: "prod", rId: "water", minLevel: 1, value: 12, }] },
        algaeOilFarm: [{ attrib: "prod", rId: "oil", minLevel: 1, value: 12, }] },
        electrolyzer: [
            { attrib: "prod", rId: "hydrogen", minLevel: 1, value: 12 },
            { attrib: "prod", rId: "water", minLevel: 1, value: 12 },
        ],
        submergedOilRefinery: [{ attrib: "prod", rId: "fuel", minLevel: 1, value: 8 }, { id: "oil", minLevel: 1, value: 8, }] },
        nanotubeDome: [{ attrib: "prod", rId: "nanotube", minLevel: 1, value: 8, }] },
        oceanographicCenter: [{ attrib: "researchPoint", minLevel: 1, value: 12 } },
      ],
      unlocks: { waterPump: 1, submergedMetalMine: 1, submergedSandMine: 1, electrolyzer: 1 },
      isVisible(state) { return state.isResearchUnlocked('chemical') && state.planets["aequoreas"].civId == "human" },
    },
    { id: "rhodium", researchPoint: 1e9, mult: 2.2, reqs:{ chemical: 17 },
      buildingBonuses: {
        rhodiumExtractor: [
            { attrib: "prod", rId: "rhodium", minLevel: 2, value: 12 },
            { attrib: "prod", rId: "titanium", minLevel: 2, value: 12, },
        ],
        rhodiumSandSmelter: [{ attrib: "prod", rId: "silicon", minLevel: 2, value: 8, }] },
        sandSmelter: [{ attrib: "prod", rId: "silicon", minLevel: 2, value: 8, }] },
        submergedSandMine: [{ attrib: "prod", rId: "silicon", minLevel: 2, value: 8, }] },
      ],
      unlocks: { rhodiumExtractor: 1, rhodiumSandSmelter: 1 },
      isVisible(state) { return state.isResearchUnlocked('chemical') && state.planets["tsartasis"].civId == "human" },
    },
    { id: "osmium", researchPoint: 5e9, mult: 2.2, reqs:{ rhodium: 2 },
      buildingBonuses: {
        osmiumExtractor: [{ attrib: "prod", rId: "osmium", minLevel: 1, value: 5, }] },
        metallokoptaClonator: [{ attrib: "prod", rId: "mkembryo", minLevel: 1, value: 5, }] },
        submergedSandMine: [{ attrib: "prod", rId: "osmium", minLevel: 2, value: 8, }] },
      ],
      unlocks: { osmiumExtractor: 1, metallokoptaClonator: 1, servant: 1 },
      isVisible(state) { return state.isResearchUnlocked('rhodium') && state.planets["shin"].civId == "human" },
    },
    { id: "quantum", researchPoint: 2e9, mult: 2.2, reqs:{ halean: 2 },
      buildingBonuses: {
        particleAccelerator: [{ attrib: "prod", rId: "antimatter", minLevel: 1, value: 12, }] },
      ],
      unlocks: { particleAccelerator: 1, antimatterCollider: 1, angerPerseus: 3 },
      isVisible(state) { return state.isResearchUnlocked('halean') && state.planets["zhura"].civId == "human" },
    },
    { id: "secret", researchPoint: 5e10, mult: 100, reqs:{ quantum: 2, nuclear: 14, rhodium: 3 }, max: 2,
      unlocks: { wahrianTimeMachine: 1, spaceGateAlpha: 1, spaceGateBeta: 2 },
      isVisible(state) { return state.isResearchUnlocked('quantum') && state.isResearchUnlocked('nuclear') && state.isResearchUnlocked('rhodium') && state.planets["zhura"].civId == "human" },
    },
    { id: "karanartofwar", researchPoint: 2e11, mult: 2.2, reqs:{ artofwar: 12 }, max: 111,
      shipBonuses: [
        vitha: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        zb03: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        zb04: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        ark22: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        ark55: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        foxar: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        skydragon: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        zb22: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        babayaga: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        zb50: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        luxis: [{ attrib: "cost", minLevel: 1, cost: -8, piercing: 30 }],
        muralla: [{ attrib: "cost", minLevel: 1, cost: -8, weight: 50, combatWeight: 50 }],
        siber: [{ attrib: "cost", minLevel: 1, cost: -8, piercing: 30 }],
        alkantara: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        auxilia: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        servant: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        orionCargo: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        angerPerseus: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        medusaMiner: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        andromeda: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        munya: [{ attrib: "cost", minLevel: 1, cost: -8 }],
        soulAndromeda: [{ attrib: "cost", minLevel: 1, cost: -8 }],
      ],
      isVisible(state) { return state.isResearchUnlocked('artofwar') && state.planets["alfari"].civId == "human" },
    },
    { id: "spacemining", researchPoint: 2e12, mult: 3, reqs:{ secret: 1 }, planet: "swamp", max: 100,
      shipBonuses: [
        andromeda", minLevel: 1, storage: 8 },
      ],
      unlocks: [
        { level: 1, ships:["medusaMiner", "andromeda"] },
      ],
      isVisible(state) { return state.isResearchUnlocked('secret') && state.planets[""].civId == "human" },
    },
    { id: "karannuclear", researchPoint: 25e10, mult: 2.2, reqs:{ quantum: 6 }, planet: "xeno",
      buildingBonuses: {
        thoriumExtractor: [{ attrib: "prod", rId: "thorium", minLevel: 2, value: 25 }, { id: "caesium", minLevel: 2, value: 25, }] },
        thoriumReactor", energy:{ minLevel:1, value: 5 } },
        thoriumReactor2", energy:{ minLevel:1, value: 5 } },
        pressurizedAmmoniaReactor", energy:{ minLevel:1, value: 5 } },
        pressurizedWaterReactor", energy:{ minLevel:1, value: 5 } },
        karanLaboratory: [{ attrib: "researchPoint", { minLevel:2, value: 20 } },
      ],
      unlocks: [
        { level: 1, buildings:["thoriumExtractor", "thoriumReactor", "thoriumReactor2", "karanLaboratory", "karanLaboratory2"] },
      ],
      isVisible(state) { return state.isResearchUnlocked('quantum') && state.planets[""].civId == "human" },
    },
    { id: "ammonia", researchPoint: 6e11, mult: 2.2, reqs:{ hydro: 20 }, planet: "auriga",
      buildingBonuses: {
        ammoniaExtractor", prod:{ ammonia:{ minLevel:2, value: 25 } } },
        ammoniaRefrigerator", prod:{ coolant:{ minLevel:2, value: 25 } } },
        ammoniaElectrolyzer", prod:{ ammonia:{ minLevel:2, value: 25 }, hydrogen:{ minLevel:2, value: 25 } } },
        ammoniaExtractor: [{ attrib: "researchPoint", { minLevel:1, value: 12 } },
      ],
      unlocks: [
        { level:1, buildings:["ammoniaExtractor", "ammoniaElectrolyzer", "ammoniaRefrigerator", "pressurizedAmmoniaReactor", "ammoniaAirship"] },
      ],
      isVisible(state) { return state.isResearchUnlocked('hydro') && state.planets[""].civId == "human" },
    },
    { id: "protohalean", researchPoint: 5e12, mult: 2.2, reqs:{ karannuclear: 20 }, planet: "asun",
      buildingBonuses: {
        qasersAssembler", prod:{ qaser:{ minLevel:1, value: 12 } } },
        superfluidsCenter: [{ attrib: "researchPoint", { minLevel:3, value: 25 } },
      ],
      shipBonuses: [
        ark22", minLevel: 1, power: -80 },
        ark55", minLevel: 1, power: -80 },
        foxar", minLevel: 1, power: -80 },
        skydragon", minLevel: 1, power: -80 },
        orionCargo", minLevel: 1, power: -80 },
        andromeda", minLevel: 1, power: -80 },
      ],
      unlocks: [
        { level: 1, buildings:["qasersAssembler"], ships:["munya"] },
        { level: 2, buildings:["superfluidsCenter"] },
      ],
      isVisible(state) { return state.isResearchUnlocked('karannuclear') && state.planets[""].civId == "human" },
    },
    { id: "darkmatter", researchPoint: 15e13, mult: 2.2, reqs:{ protohalean: 1 },
      buildingBonuses: {
        darkmatterRefiner", prod:{ darkmatter:{ minLevel:1, value: 25 } } },
      ],
      unlocks: [
        { level: 1, buildings:["darkmatterRefiner"], ships:["soulAndromeda"] },
      ],
      isVisible(state) { return state.isResearchUnlocked('protohalean') },
    },
    { id: "xiranartofwar", researchPoint: 15e13, mult: 2.2, reqs:{ karanartofwar: 13 },
      buildingBonuses: {
        batteryFactory", prod:{ emptybattery:{ minLevel:1, value: 12 } } },
        batteryCharger", cost:{ steel:{ minLevel:1, value: -25 }, titanium:{ minLevel:1, value: -25 }, plastic:{ minLevel:1, value: -25 } } },
        batteryPowerPlant", cost:{ circuit:{ minLevel:1, value: -25 }, titanium:{ minLevel:1, value: -25 }, plastic:{ minLevel:1, value: -25 } } },
      ],
      isVisible(state) { return state.isResearchUnlocked('karanartofwar') && state.planets["xirandrus"].civId == "human" },
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
            
            if (bonus.prod) {
                ret[bonus.id].prod = {}
                
                bonus.prod.forEach(prod => {
                
                    if (prod.minLevel - 1 <= this.level) {
                    
                        let value = prod.value
                        if (prod.reduction) {
                            value -= prod.reduction * ((this.level + 1) - prod.minLevel)
                            if (this.level > prod.maxLevel) value = 0
                            if (value < 0) value = 0
                        }
                        
                        if (value != 0) ret[bonus.id].prod[prod.id] = value
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
            currentFleetsMenu: 'ground',
            currentFleetsGround: null,
            
            currentShipCount: 1,
            currentBuildCount: 1,
            currentDestroyCount: 1,
            
            planets: {},
            nebulas: {},
            tutorials: {},
            researches: {},
            
            bonusTime: 0,
            bonusActive: false,
        }
    },
    
    computed: {
    
        influence() {
        
            let ret = 0
            
            for (let pId in this.planets)
                if (this.planets[pId].civId == 'human') ret += this.planets[pId].influence
                
            return ret
        },
        
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
            
            for (let rId in this.currentPlanet.prod)
                if (this.isResUnlocked(rId) && this.currentPlanet.prod[rId] > 0)
                    ret[rId] = this.currentPlanet.prod[rId]
            
            return ret
        },
    },
    
    methods: {
                
        getFleetsPageState() {
            
            return null
            
            if (this.currentPage == 'fleets') return 'active'
            if (this.researches["astronomy"].level > 0) return null
            else return 'locked'
        },
        
        showFleetsPage() {
            
            this.currentPage = "fleets"
            return
            
            if (this.researches["astronomy"].level > 0) this.currentPage = "fleets"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        getMapPageState() {
            
            return null
            
            if (this.currentPage == 'map') return 'active'
            if (this.researches["astronomy"].level > 0) return null
            else return 'locked'
        },
        
        showMapPage() {
            
            this.currentPage = "map"
            return
            
            if (this.researches["astronomy"].level > 0) this.currentPage = "map"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        showResearchPage() { this.currentPage = "research" },
        
        getDiplomacyPageState() {
            
            return null
            
            if (this.currentPage == 'diplomacy') return 'active'
            if (this.timeTravelCount > 0 || this.researches["astronomy"].level > 6) return null
            else return 'locked'
        },
        
        showDiplomacyPage() {
            
            this.currentPage = "diplomacy"
            return
            
            if (this.timeTravelCount > 0 || this.researches["astronomy"].level > 6) this.currentPage = "diplomacy"
            else this.showToast("You must time travel once<br> or research Interstellar Travel level 7 first!", "error")
        },

        getTournamentPageState() {
            
            return null
            
            if (this.currentPage == 'tournament') return 'active'
            if (this.researches["astronomy"].level > 6) return null
            else return 'locked'
        },
        
        showTournamentPage() {
            
            this.currentPage = "tournament"
            return
            
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
            
            return null
            
            if (this.currentPage == 'shipyard') return 'active'
            if (this.researches["astronomy"].level > 0) return null
            else return 'locked'
        },
        
        showShipyardPage() {
            
            this.currentPage = "shipyard"
            return
            
            if (this.researches["astronomy"].level > 0) this.currentPage = "shipyard"
            else this.showToast("You must research Interstellar Travel first!", "error")
        },
        
        getMarketPageState() {
            
            return null
            
            if (this.currentPage == 'market') return 'active'
            else if (this.currentPlanet.nebula == "perseus")
                if (this.currentPlanet.buildings["tradeHub"].count > 0) return null
                else return 'locked'
            else return 'forbidden'
        },

        showMarketPage() {
            
            this.currentPage = "market"
            return
            
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
        
        toggleBonus() {
        
            this.bonusActive = !this.bonusActive
            
            if (this.bonusActive == false) this.showToast("Bonus Time disabled! Game speed normal!", "info")
            else this.showToast("Bonus Time enabled! Game speed x5!", "success")
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
                
                this.applyResearchBonuses(rId)
            }
        },
        
        canLevelUp(rId) {
        
            let ret = true
            
            let research = this.researches[rId]
            if (research.getCost() > this.researchPoint.count) ret = false
                
            return ret
        },
        
        applyResearchBonuses(rId) {
        
            let research = this.researches[rId]
            
            for (let bId in research.buildingBonuses) {
                let buildingBonuses = research.buildingBonuses[bId]
                
                buildingBonuses.forEach(bonus => {
                
                    if (research.level >= bonus.minLevel - 1) {
                    
                        let value = bonus.value
                        if (bonus.reduction) {
                            value -= bonus.reduction * ((this.level + 1) - bonus.minLevel)
                            if (research.level > bonus.maxLevel) value = 0
                            if (value < 0) value = 0
                        }
                        
                        let building =  buildingsDef.find(def => def.id == bId)
                        if (bonus.attrib == 'prod') building.prod[bonus.rId] *= (100 + value) / 100
                        else if (bonus.attrib == 'cost') building.cost[bonus.rId] *= (100 + value) / 100
                        else if (bonus.attrib == 'researchPoint') building.researchPoint *= (100 + value) / 100
                    }
                })
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
            if (def == null) console.log(bId)
            
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
            if (def == null) console.log(rId)
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
                        
                            let cost = this.planets[pId].getBuildingCost(bId, 1)
                            if (this.planets[pId].canBuy(cost) == true) {
                                
                                this.planets[pId].buildings[bId].count += 1
                                for (let rId in cost) if (cost[rId] > 1) this.planets[pId].resources[rId].count -= cost[rId]
                                
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
                
                this.tutorial = loadeddata.tutorial
                this.government = loadeddata.government
                this.researchPoint = loadeddata.researchPoint || { count:0, prod:0 }
                this.timeTravelCount = loadeddata.timeTravelCount || 0
                this.bonusTime = loadeddata.bonusTime || 0
                
                if (loadeddata.lastFrameTimeMs) {
                
                    let delta = new Date().getTime() - loadeddata.lastFrameTimeMs
                    this.bonusTime += delta
                }
                
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
                            this.applyResearchBonuses(rId)
                }
            }
            
            this.researchPoint.count = 1e12
        },

        save() {
            
            let saveddata = {
            
                tutorial: this.tutorial,
                government: this.government,
                researchPoint: this.researchPoint,
                timeTravelCount: this.timeTravelCount,
                bonusTime: this.bonusTime,
                lastFrameTimeMs: this.lastFrameTimeMs,
                
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
                
                let delta = deltaTime / 1000
                if (this.bonusActive == true && this.bonusTime > 0) {
                
                    this.bonusTime -= delta * 4000
                    if (this.bonusTime < 0) {
                    
                        this.bonusTime = 0
                        this.bonusActive = false
                    }
                    
                    delta *= 5
                }
                
                this.produceResources(delta)
                this.buildBuildings()
                
                if (this.tutorial == true) this.checkTutorial()
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
