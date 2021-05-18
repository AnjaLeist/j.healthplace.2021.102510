# j.healthplace.2021.102510
Code by Jason Settels for doi:10.1016/j.healthplace.2021.102510
Please cite this paper when using the code: 
Settels, J., & Leist, A. K. (2021). Changes in neighborhood-level socioeconomic disadvantage and older Americans’ cognitive functioning. Health & Place, 68, 102510.

Data availability:
Respondent-level variables were obtained from the National Social Life, Health, and Aging Project (NSHAP). The NSHAP is a longitudinal panel study of a representative sample of older Americans employing a complex multi-stage area probability sampling strategy and focusing on health, well-being, and interpersonal relationships. Wave 2 and wave 3 census tract-level data were obtained from the 2006–2010 and 2011–2015 American Community Surveys (ACS), respectively. For both waves, data over five years were averaged because individual years included too few respondents per census tract, and each census tract was studied across all five years. Data were made available by the Interuniversity Consortium for Political and Social Research, Ann Arbor, MI.

Funding:
This work was supported by the Canadian Social Sciences and Humanities Research Council (Insight Development Grant number 231615); and the Ontario Ministry of Research and Innovation Early Researcher Award. This research was supported by the 2020 Research Block Grant Allocation Scheme–Merit Based Funding Scheme: Incentive B, Faculty of Humanities, Education and Social Sciences, University of Luxembourg. This project has also received funding from the European Research Council (ERC) under the European Union’s Horizon 2020 research and innovation programme (grant agreement No 803239).

Code for central regressions exported from the NORC Data Enclave

/*
cognch: change in cognitive functioning through time
sdch: change in census tract-level socioeconomic disadvantage through time
gender: a respondent’s gender
ethgrp: a respondent’s race/ethnicity
analsamp: whether a respondent was included in the analytical sample
dvnonmiss: whether a respondent was not originally missing data in the dependent variable
fnlwght: the final sampling weight
tractw2: a respondent’s census tract at wave 2
popdens: a respondent’s wave 2 census tract’s logged population density (persons per square 
               mile)
msa: whether a respondent’s census tract at wave 2 was within a metropolitan statistical area, 
         which is a designation for urban location
age: a respondent’s age (in years) at wave 2
married: a respondent’s marital/relationship status at wave 2
parent: a respondent’s parental situation at wave 2
educ: a respondent’s highest level of education at wave 2
physhlth: a respondent’s self-rated physical health at wave 2
function: a respondent’s functional health at wave 2
work: a respondent’s workforce situation at wave 2
reslength: the length of time during which a respondent resided within his/her census tract at 
                 wave 2
tractch: whether a respondent relocated to a new census tract between waves 2 and 3
wave1: whether a respondent took part in the first wave of the NSHAP 
depch: change in depressive symptoms through time
netch: change in close social network size through time
physactch: change in extent of rigorous physical activity through time
*/

*First Two OLS Regressions
mi estimate: regress cognch sdch i.gender i.ethgrp if analsamp==1 & dvnonmiss==1 ///  [pweight= fnlwght], vce(cluster tractw2)

mi estimate: regress cognch sdch i.gender i.ethgrp popdens i.msa age i.married i.parent ///
i.educ i.physhlth function i.work i.reslength i.tractch i.wave1 if analsamp==1 & ///
dvnonmiss==1 [pweight= fnlwght], vce(cluster tractw2)

*Structural Equation Modelling Mediation Analysis
mi estimate (med_dep: [depch]_b[sdch]*[cognch]_b[depch]) ///
(med_net: [netch]_b[sdch]*[cognch]_b[netch]) ///
(med_physact: [physactch]_b[sdch]*[cognch]_b[physactch]) ///
(med_allthree: ([depch]_b[sdch]*[cognch]_b[depch]) + /// 
([netch]_b[sdch]*[cognch]_b[netch]) + ///
([physactch]_b[sdch]*[cognch]_b[physactch])) ///
(tot_eff: ([cognch]_b[sdch]) + ([depch]_b[sdch]*[cognch]_b[depch]) + ///
([netch]_b[sdch]*[cognch]_b[netch]) + ([physactch]_b[sdch]*[cognch]_b[physactch])), ///
cmdok: gsem (cognch <- depch netch physactch sdch i.gender i.ethgrp popdens i.msa ///
age i.married i.parent i.educ i.physhlth function i.work i.reslength i.tractch i.wave1, regress) ///
(depch <- sdch i.gender i.ethgrp popdens i.msa age i.married i.parent ///
i.educ i.physhlth function i.work i.reslength i.tractch i.wave1, regress) ///
(netch <- sdch i.gender i.ethgrp popdens i.msa age i.married i.parent ///
i.educ i.physhlth function i.work i.reslength i.tractch i.wave1, regress) ///
(physactch <- sdch i.gender i.ethgrp popdens i.msa age i.married i.parent ///
i.educ i.physhlth function i.work i.reslength i.tractch i.wave1, regress) ///
if analsamp==1 & dvnonmiss==1 [pweight= fnlwght], vce(cluster tractw2)
