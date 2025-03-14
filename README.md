# survival_analysis
data M2;

set Cr.final ;

run;

DATA M2_1;SET M2;

if death_dt^=. and death_dt<=index_dt+365 then length=death_dt-index_dt;

else length=365;

run;
proc sort data=M2_1; by sex cr;

run;
proc phreg data=M2_1 covm;

  class sex (ref='1') cr (ref='0') poor (ref='0') DIAB (ref='0') obesity (ref='0') coagulopathy (ref='0')

         abn_electrolyte (ref='0') bp (ref='0') Hypothyroidism (ref='0') ckd (ref='0')

         LIVER (ref='0') lung (ref='0') hf (ref='0') phd (ref='0') cad (ref='0')

         pvd (ref='0') av_block (ref='0') anemia (ref='0') a_fib (ref='0') a_flutter (ref='0')

         lipid (ref='0') smoking (ref='0') alcohol (ref='0') prior_stroke (ref='0')

         prior_cabg (ref='0') Prior_pci (ref='0') PMI (ref='0') race (ref='1');

   model length*death(0) = sex age cr poor DIAB obesity coagulopathy abn_electrolyte bp

                                  Hypothyroidism ckd LIVER lung hf pvd av_block anemia a_fib

                                  a_flutter lipid smoking alcohol prior_stroke prior_cabg

                                  Prior_pci PMI race/  RISKLIMITS;

run;

data _null_;

   %let url = //support.sas.com/documentation/onlinedoc/stat/ex_code/151;

   infile "http:&url/templft.html" device=url;

 

   file 'macros.tmp';

   retain pre 0;

   input;

   _infile_ = tranwrd(_infile_, '&amp;', '&');

   _infile_ = tranwrd(_infile_, '&lt;' , '<');

   if index(_infile_, '</pre>') then pre = 0;

   if pre then put _infile_;

   if index(_infile_, '<pre>')  then pre = 1;

run;

 

%inc 'macros.tmp' / nosource;

 

%ProvideSurvivalMacros

 

 

%ProvideSurvivalMacros

 

%let yOptions = label="Survival"

                linearopts=(viewmin=0.6 viewmax=1

                            tickvaluelist=(0.6 0.7 0.8 0.9 1.0));


%CompileSurvivalTemplates

ods graphics on;

proc lifetest data=m2_1 plots=survival(cb=hw test);

   time length * death(0);

   strata sex cr;

run;

ods graphics off;

proc template;

   define statgraph Stat.Lifetest.Graphics.ProductLimitSurvival;

      begingraph;

         layout overlay / yaxisopts=(label="Survival" linearopts=(viewmin=0.6 viewmax=1

                            tickvaluelist=(0.6 0.7 0.8 0.9 1.0)));

            drawsurvivalplot;

         endlayout;

      endgraph;

   end;

run;

ods graphics on;

proc lifetest data=m2_1 plots=survival(cb=hw test);

   time length * death(0);

   strata sex cr;

run;

ods graphics off;
