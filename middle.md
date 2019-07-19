// 处理前
HH:MM:SS,Ticks,L2AgentSn,usSfn,ucSubFrm,ulRBNum,usSysSfn,ucSysSubFrm,ulSchUserNum,schUserNumLimit4Phich,ulSemiSchUserNum,ulFreq,FrameIntTime,Ulsch1stBeginTime,Ulsch1stEndTime,Ulsch2ndBeginTime,Ulsch2ndEndTime,UserSchBeginTime,UserSchEndTime,DciLockedTime,PdcchTime,UlschEndTime,UlschFinishTime,UlschCorrelationTime,
17:24:41,28,0,0,0,0,0,0,0,255,0,2200,0,0,0,3109947748,3109948628,3109948760,3109949112,3109952060,3109971112,3109996104,3110009656,3110009832,
17:24:41,28,0,54,2,0,54,2,0,255,0,2200,3110544212,3110586452,3110587156,3109947748,3109948628,3109948760,3109949112,3109952060,3109971112,3111227532,3111239764,3111240028,

// 处理后
HH:MM:SS,Ticks,L2AgentSn,usSfn,ucSubFrm,ulRBNum,usSysSfn,ucSysSubFrm,ulSchUserNum,schUserNumLimit4Phich,ulSemiSchUserNum,ulFreq,FrameIntTime,Ulsch1stBeginTime,Ulsch1stEndTime,Ulsch2ndBeginTime,Ulsch2ndEndTime,UserSchBeginTime,UserSchEndTime,DciLockedTime,PdcchTime,UlschEndTime,UlschFinishTime,UlschCorrelationTime,firstTime,SecTime,ULSCHTime,PDCCHTime,MNGTime,PostTime,DLSCHTime,CorrelationTime,SCHFrasePerUsr
20:36:25,736,0,0,0,0,0,0,0,255,0,2200,0,0,0,1307632.753,1307633.553,1307633.573,1307633.953,1307634.393,1307647.313,1307648.173,1307648.473,1307648.593,0,0.8,0.38,12.92,0.86,0.3,1307632.753,0.12,0
20:36:25,736,0,507,6,110,507,6,43,255,0,2200,2877394812,9.82,49.12,455.76,521.54,521.58,835.8,836.42,851.7,876.84,924.74,924.8,39.3,65.78,314.22,15.28,25.14,47.9,406.64,0.06,7.30744186

// 59_Pro.py
```
import os

def f51MngPro(fileName,fOutName):
    fout = open(fOutName,'a')
    fi = open(fileName)
    haerAdded =",firstTime,SecTime,ULSCHTime,PDCCHTime,MNGTime,PostTime,DLSCHTime,SCHFrasePerUsr"+'\n'
    headline = fi.readline()
    headline = headline[0:headline.__len__()-2]+haerAdded
    fout.write(headline)
    sStart = headline.split(',')
    indexFreq = sStart.index("ulFreq")
    indexFrmInt = sStart.index("FrameIntTime")
    ulSubFrameIndex = sStart.index("ucSubFrm");
    IndexStart=[]
    IndexEnd =[]
    #first
    IndexStart.append(sStart.index("Ulsch1stBeginTime"))
    IndexEnd.append(sStart.index("Ulsch1stEndTime"))
    #second
    IndexStart.append(sStart.index("Ulsch2ndBeginTime"))
    IndexEnd.append(sStart.index("Ulsch2ndEndTime"))
    #ulsch
    IndexStart.append(sStart.index("UserSchBeginTime"))
    IndexEnd.append(sStart.index("UserSchEndTime"))
    # PDCCH
    IndexStart.append(sStart.index("DciLockedTime"))
    IndexEnd.append(sStart.index("PdcchTime"))
    # mng
    IndexStart.append(sStart.index("PdcchTime"))
    IndexEnd.append(sStart.index("UlschEndTime"))
    # Post
    IndexStart.append(sStart.index("UlschEndTime"))
    IndexEnd.append(sStart.index("UlschFinishTime"))
    # DLSCH time
    IndexStart.append(sStart.index("Ulsch1stEndTime"))
    IndexEnd.append(sStart.index("Ulsch2ndBeginTime"))
    #perUsr
    indexUser = sStart.index("ulSchUserNum")
    UlschTime = 0
    for eachline in fi:
        sStart = eachline.split(',')
        if(sStart[0] == "HH:MM:SS"):
            continue
        index = indexFrmInt + 1
        ulFrmInt = int(sStart[indexFrmInt])
        ulFre = int(sStart[indexFreq])
        ulSubFrame = int(sStart[ulSubFrameIndex])
        maxLen = sStart.__len__()-1
        strS = ","
        while index < maxLen:
            sStart[index] = str((int(sStart[index])-ulFrmInt)/ulFre)
            index = index +1
        sStart.pop()
        for i in range(0,IndexStart.__len__(),1):
            indS = IndexStart[i]
            indE = IndexEnd[i]
            if(ulSubFrame == 2 or ulSubFrame == 7):
                indE = IndexEnd[4]
            #dat = int(sStart[indE]) - int(sStart[indS])
            dat = float(sStart[indE]) - float(sStart[indS])
            if(i == 2):
                UlschTime = dat
            sStart.append(str(dat))
        indS = int(sStart[indexUser])
        if(indS != 0):
            sStart.append(str(float(UlschTime)/float(indS)))
        else:
            sStart.append(str(0))
        sStart.append(str('\n'))
        Newline = strS.join(sStart)
        fout.write(Newline)
    fout.close()
    fi.close()

cwd = os.getcwd()
filelist = os.listdir(cwd)
for fileN in filelist:
    curfile = cwd+"\\"+fileN
    if os.path.isfile(curfile) and 'Dest_T059' in curfile and 'csv' in curfile and 'New' not in curfile:
        print(curfile)
        OutFile = cwd+"\\New_"+fileN
        f51MngPro(curfile,OutFile)
```        
        
// 59_Pro_ubbpg.py
```
import os

def f51MngPro(fileName,fOutName):
    fout = open(fOutName,'a')
    fi = open(fileName)
    haerAdded =",firstTime,SecTime,ULSCHTime,PDCCHTime,MNGTime,PostTime,DLSCHTime,CorrelationTime,SCHFrasePerUsr"+'\n'
    headline = fi.readline()
    headline = headline[0:headline.__len__()-2]+haerAdded
    fout.write(headline)
    sStart = headline.split(',')
    indexFreq = sStart.index("ulFreq")
    indexFrmInt = sStart.index("FrameIntTime")
    ulSubFrameIndex = sStart.index("ucSubFrm");
    IndexStart=[]
    IndexEnd =[]
    #first
    IndexStart.append(sStart.index("Ulsch1stBeginTime"))
    IndexEnd.append(sStart.index("Ulsch1stEndTime"))
    #second
    IndexStart.append(sStart.index("Ulsch2ndBeginTime"))
    IndexEnd.append(sStart.index("Ulsch2ndEndTime"))
    #ulsch
    IndexStart.append(sStart.index("UserSchBeginTime"))
    IndexEnd.append(sStart.index("UserSchEndTime"))
    # PDCCH
    IndexStart.append(sStart.index("DciLockedTime"))
    IndexEnd.append(sStart.index("PdcchTime"))
    # mng
    IndexStart.append(sStart.index("PdcchTime"))
    IndexEnd.append(sStart.index("UlschEndTime"))
    # Post
    IndexStart.append(sStart.index("UlschEndTime"))
    IndexEnd.append(sStart.index("UlschFinishTime"))
    # DLSCH time
    IndexStart.append(sStart.index("Ulsch1stEndTime"))
    IndexEnd.append(sStart.index("Ulsch2ndBeginTime"))
    #perUsr
    indexUser = sStart.index("ulSchUserNum")
    UlschTime = 0
	#UlschCorrelationTime
    IndexStart.append(sStart.index("UlschFinishTime"))
    IndexEnd.append(sStart.index("UlschCorrelationTime"))
	
    for eachline in fi:
        sStart = eachline.split(',')
        if(sStart[0] == "HH:MM:SS"):
            continue
        index = indexFrmInt + 1
        ulFrmInt = int(sStart[indexFrmInt])
        ulFre = int(sStart[indexFreq])
        ulSubFrame = int(sStart[ulSubFrameIndex])
        maxLen = sStart.__len__()-1
        strS = ","
        while index < maxLen:
            sStart[index] = str((int(sStart[index])-ulFrmInt)/ulFre)
            index = index +1
        sStart.pop()
        for i in range(0,IndexStart.__len__(),1):
            indS = IndexStart[i]
            indE = IndexEnd[i]
            if(ulSubFrame == 2 or ulSubFrame == 7):
                indE = IndexEnd[4]
            #dat = int(sStart[indE]) - int(sStart[indS])
            dat = float(sStart[indE]) - float(sStart[indS])
            if(i == 2):
                UlschTime = dat
            sStart.append(str(dat))
        indS = int(sStart[indexUser])
        if(indS != 0):
            sStart.append(str(float(UlschTime)/float(indS)))
        else:
            sStart.append(str(0))
        sStart.append(str('\n'))
        Newline = strS.join(sStart)
        fout.write(Newline)
    fout.close()
    fi.close()

cwd = os.getcwd()
filelist = os.listdir(cwd)
for fileN in filelist:
    curfile = cwd+"\\"+fileN
    if os.path.isfile(curfile) and 'Dest_T059' in curfile and 'csv' in curfile and 'New' not in curfile:
        print(curfile)
        OutFile = cwd+"\\New_"+fileN
        f51MngPro(curfile,OutFile)
```
