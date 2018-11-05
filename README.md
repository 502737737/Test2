ScriptDir=/home/hdp/platform_scripts/script
Log=/home/hdp/platform_scripts/script/logs
host=`hostname -i`
#Threshold= "80"
Rcpnts='vishnu.peri@ge.com, prashanth.S.kumar@ge.com, vanteru.poojitha@ge.com, gireesh.balaga@ge.com, sairam.uddanda@ge.com, sowmya.morisetty@ge.com, PavanKumar.Ravipati@ge.com, ramalokeshwara.reddy@ge.com, vamshikrishna.vytla@ge.com, aditya.peri@ge.com,vinay.ravipati@ge.com,ikram.khan@ge.com, Bhasker.Pinninti@ge.com'
#rm $ScriptDir/full_mail.out
for i in `cat $ScriptDir/hosts`;
        do
                ssh $i df -kh > $Log/$i;
        done

for s in `ls $Log`;
        do
                awk 'int($5) > 78' $Log/$s > $ScriptDir/check_$s;
                        if [[ -s $ScriptDir/check_$s ]]
                then
                                        echo -e "NODE : $s \n" >> $ScriptDir/full_mail.out
                                        cat $ScriptDir/check_$s| awk '{print $1 "   "$5"   " $6}' >> $ScriptDir/full_mail.out
                                echo -e "----------------------------------------------------------\n\n" >> $ScriptDir/full_mail.out
                        fi
done

if [[ -s $ScriptDir/full_mail.out ]]
        then
                sed -i '1i\'"Hi All, \n\nBelow are the details of space usage of the HWX Prod Cluster. Please take necessary actions. \n\n\n" $ScriptDir/full_mail.out
                cat $ScriptDir/full_mail.out |mailx -s "GE Transportation HWX Disk Usage" -r Transportation.DataLakeSupport@ge.com "$Rcpnts"
fi
#rm $Log/*
#rm $ScriptDir/check*
