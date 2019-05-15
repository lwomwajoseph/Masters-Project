# Masters-Project

from __future__ import division
from win32com.client import constants


from sklearn import svm, datasets
from sklearn.metrics import roc_curve, auc

import numpy as np
import matplotlib.pyplot as plt

import win32com.client

import pythoncom
import itertools
import scipy;

import pefile
import pydasm
import sys

import os
import fnmatch
import re
from fnmatch import fnmatch
import os, os.path
import sys,os
    #import pefile
    #import immlib
    #import peutils
import hashlib
import shutil
import urllib
from itertools import chain
from itertools import ifilter
import hashlib
import ctypes
import math
import re
from fnmatch import fnmatch
from pydasm import *





from struct import *
import win32api
from win32com.client import constants
import win32com.client
import pythoncom
import win32com.client as win32
import xlwt as xl

import pefile
import pydasm
import sys


import os
import fnmatch
import re
from fnmatch import fnmatch
import os, os.path
import sys,os
#import pefile
#import immlib
#import peutils
import hashlib
import shutil
import urllib
from itertools import chain
from itertools import ifilter
import hashlib
import string
import xlwt
import itertools



start = 0
end = 250
width = 16
t = []
p = []
signature = []
signature1 = []
x = []
x1 = []
filenam = []
bad = {}
good = {}
filelot ={}
g = [ ]
g1 = []
t = " "
t1 = " "

excel = win32.gencache.EnsureDispatch('Excel.Application')
excel.Visible = True
wb = excel.Workbooks.Add()
ws = wb.Worksheets('Sheet1')
ws.Name = 'Built with python'


filename1 = "C:\Users\User\Desktop\malicNon"
  
filename2 = "C:\Users\User\Desktop\malic"


extns1 = ('.vbs','.bat','.com')
extns = ('.tif', '.tiff','.exe','.dll')

list1 = []
list2 = []




def calculate(data4, ep_ava):
    result1 = []
    instruction1 =[]
    instruction3 =[]
    instruction4 =[]
    merged =[]
    result1 = []
    Operand1 = ""
    Opcode1 = ""
    OpcodeProb = 0
    OperandProb = 0
    #global statusOpc, statusOpe
    #global OpcodeProb, OperandProb
    
    offset = 0
    entropy = 0    
    if not data4:  
        print 0  
    for x in range(100):  
        p_x = float(data4.count(chr(x)))/len(data4)  
        if p_x > 0:  
            entropy += - p_x*math.log(p_x, 2)  
    print "entropy of a file: ",entropy

    while offset < len(data4):
        i = pydasm.get_instruction(data4[offset:], pydasm.MODE_32)
        #p = pydasm.get_mnemonic_string(data[offset:], pydasm.MODE_32)
        #print data[offset],pydasm.MODE_16
        instruction = pydasm.get_instruction_string(i, pydasm.FORMAT_INTEL, ep_ava+offset)
        instructionOpc = pydasm.get_mnemonic_string(i, pydasm.FORMAT_INTEL)
        #instructionOper = pydasm.get_operand_string(i, pydasm.FORMAT_INTEL)
        #instruction3 = pydasm.get_operand_string(i, pydasm.FORMAT_INTEL)
        #print get_instruction_string(i, pydasm.FORMAT_INTEL, ep_ava+offset)
        print ord(data4[offset]), '', hex(offset) , '', ep_ava+offset, '', instructionOpc, '', instruction
        #print ord(data[offset]), '', hex(offset) , '', data[offset:], '', ep_ava+offset, '', instruction
        nums = " ".join(["%02x" % ord(data4[offset])])
        #print nums
        if not i:
            break
        offset += i.length
        instruction1.append(instructionOpc)
        instruction3.append(nums)
        instruction4.append(instruction)
        #instruction5.append(instructionOper)
    print "list of opcode mnemonic: ",instruction1
    print "the different section startg points: ",instruction3
    print "the whole instruction having opcode and operand: ",instruction4

    for item in instruction4:
        #s2 = item.split(' ',1)[1]
        s2 = ' '.join(item.split()[1:])
        #s2 = item.split()[1:]
        print "list of operand: ", s2
        s3 = str(s2).replace('[', '').replace(']', '')
        #regi = s3.split(',')
        regi = filter(None, (s3.split(',')))
        #line.strip().split(',')
        result1.append(regi)
        #merged = list(itertools.chain.from_iterable(result1))
        #merged is a list having all items outside there own lists thus we have them not in this kind of way which is of result1 [['0x6171'], ['0x59a9']
        merged = list(itertools.chain(*result1))
    print "list of the operands only: ",merged

    
    P1moOpc, P2leOpc, countsOpc = compute(instruction1)
    #print P1moOpc, P2leOpc, countsOpc
    print "probability for more and less and count for opcode: ", P1moOpc, P2leOpc, countsOpc
    ##    statusOpc = status
    ##    countsOpc = counts
    AmaOpc = ['nop','jmp','goto','xor','jnz','dec','push','jns','jge']
    OpcodeProb, meanmal1, median = compare(AmaOpc, countsOpc)
    print "Opcode probability and meanmal1:", OpcodeProb, meanmal1, median
    
    P1moOpe, P2leOpe, countsOpe = compute(merged)
    print P1moOpe, P2leOpe, countsOpe
    print "probability for more and less and count for operand: ", P1moOpe, P2leOpe, countsOpe
    #statusOpe = status
    #countsOpe = counts
    AmaOpe = ['edi','esi','ebx', 'ebp']
    OperandProb, meanmal1, median = compare(AmaOpe, countsOpe)
    print "Operand probability and meanmal1:", OperandProb, meanmal1, median

    return P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median

def compute(instruct):
    

    global P1mo, P2le, counts, status
    counts = {}
    #counts = ""
    for x in instruct:
        #print x
        total = counts.get(x, count)
        counts[x] = counts.get(x, count)+ 1
    print "the counts dictionary for opcodes and operands: ",counts

                #Operand = compute(instruction1)
    for key, value in counts.iteritems():
        print key, value    
        ##  for y in itertools.chain.from_iterable(x):
        ##     if y not in newlist:
        ##       newlist.append(y)
    X2 = counts.values()
    Y2 = counts.keys()
    print "values and keys:", X2, Y2

    X1 = sum(counts.values())
    Y1 = len(counts.keys())

    print "sum of values and keys for dictionary",X1, Y1

    m = (X1/Y1)
    print "mean: ",m


    Acount = []
    Bcount = []
    A1opcode =[]
    B1opcode = []
    for key, value in counts.iteritems():
    #print key, value
        if value <=(m):
            Bcount.append(value)
            B1opcode.append(key)
            #print key
        if value >(m):
            Acount.append(value)
            A1opcode.append(key)
            #print key  
    print "list of the more values:", Acount
    print "sum of the more values:",sum(Acount)
    print "list of the more keys :",A1opcode
    print "list of the less values:",Bcount
    print "sum of the less values:",sum(Bcount)
    print "list of the less keys :",B1opcode

    X1 = sum(counts.values())
    P1mo = float(format(sum(Acount)/X1, '.3f'))
    print "probability for more:", P1mo
    X1 = sum(counts.values())
    P2le = float(format(sum(Bcount)/X1, '.3f'))
    print "probability for less: ", P2le

    #if  P1mo >= 0.5 and P2le < 0.5:
    #counts1 = counts
    return P1mo, P2le, counts
            
def compare(AmaOpe, countsOpe):
    probabmal = 0
    countmal = []
    TheSum = 0
    for key,value in countsOpe.iteritems():
        if key in AmaOpe:
            countmal.append(value)
    print "list of suspected operands and opcodes: ",countmal
    Z1 = sum(countmal)
    Q1 = sum(countsOpe.values())
    print "sum for suspected list and operand and opcode values:", Q1,Z1
    #probmal = (Z1/Q1)
    #print probmal

    #print (Y1/X1)
    try:
        meanmal1 = format((Q1/Z1), '.3f')
        print "the mean of malicious list: ",meanmal1
    except ZeroDivisionError, e:
        meanmal1 = e
    print "the mean of the malicious list: ",meanmal1
    median = Q1/2
        
    probabmal = float(format(float(Z1/Q1),'.3f'))
    print "probability for discovering list elements in the dictionary upon comparing: ",probabmal
    return probabmal, meanmal1, median

def state(P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median):
    func1a, func2b = 0, 0
    func3c, func4d = 0, 0
    func5e, func6f = 0, 0
      
    func11a, func22b = 0, 0
    func33b, func44d = 0, 0
    func55e, func66f = 0, 0
    
    if (P1moOpc < 0.5)and (P2leOpc >= 0.5) :
        func1a = 1            
    elif (P1moOpc <= 0.5) and (P2leOpc > 0.5):
        func11a = 1
    if (P1moOpe <= 0.5)and (P2leOpe > 0.5) :
        func2b = 1
    elif (P1moOpe <= 0.5) and (P2leOpe > 0.5):
        func22b = 1
    print "Opcode and operand probability:",OpcodeProb, OperandProb
    if OpcodeProb >= 0.5:
        func3c = 1
    else:
        func33c = 1
    if OperandProb >= 0.5:
        func4d = 1
    else:
        func44d = 1
    print "entropy and mean:", entropy, meanmal1
    if entropy <= 4:
        func5e = 1
    else:
        func55e = 1
    if meanmal1 < (median/2):
        func6f = 1
    else:
        func66f = 1

    FunctionMalic = func1a + func2b + func3c + func4d + func5e + func6f 
    print "Total for rule set for malicious files:",FunctionMalic    
    
    FunctionBegnin = func11a + func22b + func33c + func44d + func55e + func66f
    print "Total for rule set for begnine files:", FunctionBegnin   

    return FunctionMalic, FunctionBegnin    





def compared(list1, list2):
    probabmal = 0
    list3 = []
    list4 = []
    TheSum = 0
    for item in list1 :
        if item in list2:
            list3.append(item)
        if item not in list2:
            list4.append(item)    
    
    T1 = len(list3)
    F1 = len(list4)

    return T1, F1




def signaturestudy(filenamez):
    filenam = []
    filelot = {}
    for root, dirnames, filenames in os.walk(filenamez):
        for filename in filenames:
            #print filename
            #filenamBenign.append(filename)
            if filename.endswith((extns)):
                print filename 

                filenam.append(filename)
                path1 = os.path.join(root,filename)
                f = open(path1, "rb")
                #print f
                data = f.read()
                f.seek(start)
                #print f.seek(start)
                chunk = f.read(end-start)
                print chunk[:10]
                t = " "
                #x =[]
                for line in chunk:
                    nums = ["%02x" % ord(c) for c in line]
                    print " ".join(nums),
                    j = " ".join(nums)
                    #print j,
                    t += " " + j
        
                    #g.append(" ".join(nums))
                    #t = " ".join(nums)
                print "#####################################################"  
                print t[:10]+ '\n'
      #print g1
        #break
        #print nums
      #print "\n"
      #t = " ".join(nums)
        #print t,
      #for y in t:
      #print y
                signature1 = hashlib.md5(t).hexdigest()
                print signature1 
                x.append(signature1)
                filelot = dict(zip(filenam,x))
                print x[0:5]
                print "this is", filelot 
                print "################################################"
    return filelot, x, filenam, root


Goodfinal = []
Badfinal = []


start = 0
end = 250
width = 16
t = []
p = []
signature = []
signature1 = []
x = []
x1 = []
filenam = []
bad = {}
good = {}
filelot ={}
g = [ ]
g1 = []
t = " "
t1 = " "

#extns1 = ()

bytes = 8
#format = FORMAT_INTEL

define = []
define1 = []
newlist = []
nums = []
instruction1 = []
instruction2 = []
instruction3 = []
instruction4 = []

FileMalic = []
FileBegnin = []
fileList = []


counts = {}
count = 0
Acount = []
Bcount = []
A1opcode =[]
B1opcode = []
seen1 = [ ]
P2le = 0
P1mo = 0
ep_ava = 0
#data = 0
start = 0
end = 250
width = 16
t = []
signature = []
line = " "
data4 = ""

executable1 = ""
executable2 = ""
executable3 = ""
status = ""

ep_avasec = 0
ep_avabegn = 0



good, x1, filenamNon, root = signaturestudy(filename1)
N = len(filenamNon)
print N 

bad, x2, filenamMalic, root1 = signaturestudy(filename2)
P = len(filenamMalic)
print P 



for d in range (len(x1)):
    x1, x1[d] + '\n'

print x1, x1[d] + '\n'
print '##################################'    
#for d1 in range (len(good)):
    #print good[d1]
i=2
for key, value in good.items():
    #print i
    ws.Cells(i,7).Value = key
    ws.Cells(i,8).Value = value
    ws.Cells(1,7).Value = 'BenignfileName'
    ws.Cells(1,8).Value = 'BenignfileSignature'
    i+=1
print '##################################'
print '##################################'
print '##################################'
for p in range (len(x2)):
    x2, x2[p] + '\n'

print x2, x2[p] + '\n'
print '##################################'    
##for p1 in range (len(bad)):
##    print bad[p1]
j=2
for key, value in bad.items():
    ws.Cells(j,10).Value = key
    ws.Cells(j,11).Value = value
    ws.Cells(1,10).Value = 'MaliciousfileName'
    ws.Cells(1,11).Value = 'MaliciousSignature'
    #ws.Cells(i,13).Value = bad.items()
    j+=1




##SEARCHING FOR GOOD IN BAD

for key, value in good.iteritems():
    #print key, value
    k = 2
    for key1, value1 in bad.iteritems():
        if value in bad[key1]:
            status = "malicious"
            Badfinal.append(key)
            ws.Cells(1,13).Value = 'TrueMaliciousfileName'
            ws.Cells(1,14).Value = 'TrueMaliciousSignature'
            ws.Cells(k,13).Value = key
            #ws.Cells(k,15).Value = good[key]
            ws.Cells(k,14).Value = value
            
            #print key, good[key]
            #print key, value + '\n'
            #print "malicious"
            
            define = Badfinal, status
            print status
            print '###############malicious'
            print '###############malicious'
            print '###############malicious'
            speaker = win32com.client.Dispatch("SAPI.SpVoice")
            speaker.Speak("This file is infected OR deadly NO further action is required ")
            print status
            print "This file is infected OR deadly NO further action is required"
            #print Badfinal[:5]
            #k+=1
        #elif value not in bad[key1]:
        else:
            status = "Nonmalicious"
            Goodfinal.append(key)
            ws.Cells(1,16).Value = 'TrueNonMaliciousfileName'
            ws.Cells(1,17).Value = 'TrueNonMaliciousSignature'
            ws.Cells(k,16).Value = key
            #ws.Cells(k,15).Value = good[key]
            ws.Cells(k,17).Value = value
            
            define1 = Goodfinal, status
            print status


    

            
            datasec = ""
            databegn = ""
            ep_avasec = 0
            ep_avabegn = 0
            executable1 = ""
            executable2 = ""
            executable3 = ""
            ep = 0
            func0 = 0
            func00 = 0
            instruction1 =[]
            instruction3 =[]
            instruction4 =[]
            result1 = []
            merged = []
    
            probMalic = 0
            probMalicSec = 0
            probMalicNExe = 0

            if key.endswith((extns)):
          
                print key
                fileList.append(key)
                print fileList
                path1 = os.path.join(root,key)
        
                f = open(path1, "rb")
                #filesize = os.path.getsize(f)
                #print filesize
                data = f.read()
                print f.seek(start)
                #print f.seek(start)
                chunk = f.read(end-start)
                t1= ""


                for line in chunk:
                    from string import printable
                    if line in printable[:-15]:
                    #print "".join(line),
                        T1 = "".join(line)
                    #print T1,
                        t1 +=  T1
                print "hexadecimal chunk:", t1,
            
            
                if re.search(r'^MZ.*', t1, re.M|re.I):
                    print "match"
                    func0 = 1
                else:
                    print "No match!!"
                    func00 = 1

                pe =  pefile.PE(path1)
                ep = pe.OPTIONAL_HEADER.AddressOfEntryPoint
                ep_avabegn = ep+pe.OPTIONAL_HEADER.ImageBase

                databegn = pe.get_memory_mapped_image()[ep:ep+100]
                #print data
                #data += data1
                P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median = calculate(databegn, ep_avabegn)
                #print "Outside the function : ", executable1 
                print "Outside the function : ", P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median
                FunctionMalic, FunctionBegnin = state(P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median)
                FunctionMalicExe = FunctionMalic + func0
                print "Function for Executable: ", FunctionMalicExe
            
    
                #probMalic = float(format(FunctionMalicExe)/6, '.3f')
                #probMalic = format((FunctionMalicExe)/6, '.3f'))
                probMalic = float(format(float(FunctionMalicExe)/7, '.3f'))
                print "Probability for Executable: ", probMalic


                for section in pe.sections:
                    if section.Name == '.text\x00\x00\x00':
                    #function = 
                        print (section.Name, hex(section.VirtualAddress),
                            hex(section.Misc_VirtualSize), section.SizeOfRawData )
                        secttnam = hex(section.VirtualAddress)
                        sectsiz = (section.SizeOfRawData)
                        ep = (section.VirtualAddress)
                        ep_avasec = ep+ (section.SizeOfRawData)
                        print ep
                        datasec = pe.get_memory_mapped_image()[ep:ep+100]
                        P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median = calculate(datasec, ep_avasec)
                        print P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median
                        FunctionMalicSec, FunctionBegnin = state(P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median)
                        print "Function for Executable section : ", FunctionMalicSec
                        #probMalicSec = float(format(FunctionMalicSec)/6, '.3f')
                        #probMalicSec = format((FunctionMalicSec)/6, '.3f'))
                        probMalicSec = float(format(float(FunctionMalicSec)/6, '.3f'))
                        print "Probability for Executable text section : ", probMalicSec


                print probMalic, probMalicSec
                if probMalic >= 0.5 or probMalicSec >= 0.5:
                    status = "malicious"
                    FileMalic.append(key)
                    ctypes.windll.user32.MessageBoxA(0, "Malicious", "LWOMWA JOSEPH", 6)
                    speaker = win32com.client.Dispatch("SAPI.SpVoice")
                    speaker.Speak("This file is infected and deadly further action is required so please endeavor to delete ")
                    print status
                    print "This file is infected and deadly further action is required so please endeavor to delete "
                    print "################################################################################"
                    print "################################################################################"

                else:   
                    status = "Nonmalicious"     
                    FileBegnin.append(key)
                    speaker = win32com.client.Dispatch("SAPI.SpVoice")
                    speaker.Speak("This file is NOT infected OR deadly NO further action is required ")
                    print status
                    print "This file is NOT infected OR deadly NO further action is required "
                    print "################################################################################"
                    print "################################################################################"

            elif key.endswith((extns1)):
                instruction1 =[]
##              instruction3 =[]
##              instruction4 =[]
                print filename
                fileList.append(key)
                #print fileList
                path1 = os.path.join(root,key)
                f = open(path1, "rb")
                #print f
                data1 = f.read()
                f.seek(start)
                #print f.seek(start)
                databegn = f.read(end-start)

                t1= ""
                for line in databegn:
                    from string import printable
                    if line in printable[:-15]:
                        #print "".join(line),
                        T1 = "".join(line)
                        #print T1,
                        t1 +=  T1
                print t1,
            
                if re.search(r'^MZ.*', t1, re.M|re.I):
                    print "match"
                    func0 = 1
                else:
                    print "No match!!"
                    func00 = 1

                P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median = calculate(databegn, ep_avabegn)
                #print "Outside the function : ", executable1 
                print "Outside the function : ", P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median
                status = state(P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median)
                FunctionMalic, FunctionBegnin = state(P1moOpc, P2leOpc, P1moOpe, P2leOpe, OpcodeProb, OperandProb, entropy, meanmal1, median)
                FunctionMalicNExe = FunctionMalic + func00
                print "Function for Non Executable: ", FunctionMalicNExe
         
                #probMalicNExe = float(format(FunctionMalicNExe)/6, '.3f')
                #probMalicNExe = format((FunctionMalicNExe)/6, '.3f'))
                probMalicNExe = float(format(float(FunctionMalicNExe)/7, '.3f'))
##
        
                print "Probability for Non Executable : ", probMalicNExe
                if probMalicNExe >= 0.5:
                    status = "malicious"
                    FileMalic.append(key)
                
                    speaker = win32com.client.Dispatch("SAPI.SpVoice")
                    speaker.Speak("This file is infected and deadly further action is required so please endeavor to delete ")
                    ctypes.windll.user32.MessageBoxA(0, "Malicious", "LWOMWA JOSEPH", 6)
                    print status
                    print "This file is infected and deadly further action is required so please endeavor to delete "
                    print "################################################################################"
                    print "################################################################################"
                else:
                    status = "Nonmalicious"     
                    FileBegnin.append(key)
                    #ctypes.windll.user32.MessageBoxA(0, "Malicious", "LWOMWA JOSEPH", 6)
                    speaker = win32com.client.Dispatch("SAPI.SpVoice")
                    speaker.Speak("This file is NOT infected OR deadly NO further action is required ")
                    print status
                    print "This file is NOT infected OR deadly NO further action is required"
                    print "################################################################################"
                    print "################################################################################"    
            elif not all( (key.startswith(extns1), key.endswith(extns)) ):
                #continue
                print('Are you try')
        
                print key, "this file format bearly carries malware"
    
    print FileMalic
    print FileBegnin

                                
            #ws.Cells(k,14).Value = "Non"
            #ws.Cells(k,15).Value = good[key]
            #ws.Cells(k,15).Value = "Non"
            
            #print Goodfinal[:5]

print '###########################'            
print "list of bad files : ", Badfinal
print "list of bad files : ", define
print '###########################' 
print "list of good files : ", Goodfinal
print "list of bad files : ", define1
print '###########################'
print '###########################'
print '###########################'


if status == 'Nonmalicious':
    print 1
elif status =='malicious':
    print 2

print filenamNon 
print filenamMalic


print N, P


Goodfinal1 = FileBegnin + Goodfinal

Badfinal1  = FileMalic +  Badfinal



TN, FN = compared(Goodfinal1, filenamNon)
print TN, FN

TP, FP = compared(Badfinal1, filenamMalic)
print "true positive and true negative : ", TP, FP


# CALCULATING THE SENSITIVITY
try:
    TPR = TP/(TP + FN)
    print "true positive rate : ", TPR
except ZeroDivisionError, e:
    TPR = e
            

# CALCULATING THE SPECIFICITY
try:
    spc = TN/(FP + TN)
    print "specificity : ", spc
except ZeroDivisionError, e:
    spc = e

# CALCULATING THE FALLOUT OR FPR
try:
    FPR = FP/(FP + TN)
    print "false positive rate : ", FPR
except ZeroDivisionError, e:
    FPR = e    

# CALCULATING THE ACCURACY
try :
    acc = ((TP + TN)/(P + N))
    print "accuracy : ", acc
except ZeroDivisionError, e:
    acc = e    

# NEGATIVE PREDICTIVE VALUE
try :
    npv = (TN/(TN+FN))
    print "negative predictive value : ", npv
except ZeroDivisionError, e:
    npv = e    

# PRECISION/POSITIVE PREDICTIVE VALUE
try :
    ppv = TP/(TP+FP)
    print "positive predictive value : ", ppv
except ZeroDivisionError, e:
    ppv = e    

# FALSE DETECTION RATE
try:
    fdr = FP/(TP+FP)      
    print "false detection rate : ", fdr
except ZeroDivisionError, e:
    fdr = e    

#THRE1.append(THRE)
TPR1.append(TPR)
FPR1.append(FPR)
SPC.append(spc)
ACC.append(acc)
NPV.append(npv)
PPV.append(ppv)
FDR.append(fdr)
    
print"this the threshold", THRE1
print"this is specificity", SPC
print"this is accuracy", ACC
print"this is negative predicative value", NPV
print"this is positive predicative value", PPV
print"false detection rate", FDR
print"true positive rate", TPR1
print"false postive rate", FPR1



fpr = [0] + FPR1
tpr = [0] + TPR1
print fpr
print tpr

# Plot of a ROC curve for a specific class
plt.figure()
roc_auc= auc(fpr, tpr)
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.0])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('Receiver operating characteristic example')
plt.legend(loc="lower right")
plt.show()



##
### CALCULATING THE SENSITIVITY
##TPR = (TP/(TP + FN))
##print "true positive rate : ", TPR
##
### CALCULATING THE SPECIFICITY
##spc = (TN/(FP + TN))
##print "specificity : ", spc
##
### CALCULATING THE FALLOUT OR FPR
##FPR = (FP/(FP + TN))
##print "false positive rate : ", FPR
##
### CALCULATING THE ACCURACY
##acc = (TP + TN)/(P + N)
##print "accuracy : ", acc
##
### NEGATIVE PREDICTIVE VALUE
##npv = TN/(TN+FN)
##print "negative predictive value : ", npv
##
### PRECISION/POSITIVE PREDICTIVE VALUE
##ppv = (TP/(TP+FP))
##print "positive predictive value : ", ppv       
##
### FALSE DETECTION RATE
##fdr = (FP/(TP+FP))       
##print "false detection rate : ", fdr
##
##fpr = [0,FPR, npv, 2]
##tpr = [0,TPR, ppv ,2]
##
### Plot of a ROC curve for a specific class
##plt.figure()
##roc_auc= auc(fpr, tpr)
##plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
##plt.plot([0, 2], [0, 2], 'k--')
##plt.xlim([0.0, 2.0])
##plt.ylim([0.0, 2.0])
##plt.xlabel('False Positive Rate')
##plt.ylabel('True Positive Rate')
##plt.title('Receiver operating characteristic example')
##plt.legend(loc="lower right")
##plt.show()

            
##
##doc = xl.Workbook() #Create document
###Add a worksheet
##sheet = doc.add_sheet("Some Sheet")
##
##for i in range(9):
###for x in signature1:
###Write 2 columns
##    sheet.write(i, 0, x[i])
##    #sheet.write(i, 1, good.key[i])
##    sheet.write(i, 2, x1[i])
##    #Write result (SUM for example)
##    #sheet.write(i, 2, xl.Formula("SUM(A%d:B%d)" % (i+1, i+1)))
##doc.save("joseph.xls")
##


















##
##
##start = 0
##end = 100
##width = 16
##t = []
##p = []
##signature = []
##signature1 = []
##x = []
##
##filename1 = 'C:\Users\User\Desktop\desktop items\GOOD JOSEPH\malwareand benign'
##  
##
##
##
##for root, dirnames, filenames in os.walk(filename1):
##  for filename in filenames:
##    print filename
##    path1 = os.path.join(root,filename)
##    f = open(path1, "rb")
##    print f
##    data = f.read()
##    f.seek(start)
##    print f.seek(start)
##    chunk = f.read(end-start)
####gap = width - (len(chunk) % width)
####chunk += gap * '\000'
###while chunk:
####  chunk[:width]
####  chunk = chunk[width:]
##      #print chunk
##    for line in chunk:
##      #print line 
##      nums = ["%02x" % ord(c) for c in line]
##      #txt = [fixchar(c) for c in line]
##      #print 
##      print " ".join(nums), 
##      t = " ".join(nums) 
##      #print t
##    for y in t:
##        signature1 = hashlib.md5(y).hexdigest()
##        print signature1
##        #print hashlib.md5(y).hexdigest()
##        #speaker = win32com.client.Dispatch("SAPI.SpVoice")
##        #speaker.Speak(signature1)
##        x.append(signature1)
##print x
##
##for p in range (len(x)):
##    print x[p]
##       
##doc = xl.Workbook() #Create document
###Add a worksheet
##sheet = doc.add_sheet("Some Sheet")
##
##for i in range(9):
###for x in signature1:
###Write 2 columns
##    sheet.write(i, 0, x[i]) 
##    #sheet.write(i, 1, y)
##    #Write result (SUM for example)
##    #sheet.write(i, 2, xl.Formula("SUM(A%d:B%d)" % (i+1, i+1)))
##doc.save("joseph.xls")
##
##
##
##

##excel = win32.gencache.EnsureDispatch('Excel.Application')
##excel.Visible = True
##wb = excel.Workbooks.Add()
##ws = wb.Worksheets('Sheet1')
##ws.Name = 'Built with python'
###ws.Cells(1,1).Value = 'Hello Excel'
###print ws.Cells(1,1).Value
##for i in range(1,10):
##    print i
##    ws.Cells(i,2).Value = x[i]  # Don't do this
##




##
##ws.Cells(1,1).Value = 'Hello Excel'
##print ws.Cells(1,1).Value
##for i in range(1,10):
##    print i
##    ws.Cells(i,2).Value = x[i]  # Don't do this
##    ws.Cells(i,5).Value = x1[i]
##
##    ws.Cells(i,7).Value = key
##
##    # Don't do this
##    ws.Cells(i,9).Value = bad.items()
##
##
##
##
##
##
##import win32com.client as win32
##excel = win32.gencache.EnsureDispatch('Excel.Application')
##excel.Visible = True
##wb = excel.Workbooks.Add()
##ws = wb.Worksheets('Sheet1')
##ws.Name = 'Built with python'
##ws.Cells(1,1).Value = 'Hello Excel'
##
##
##
###ws.Labels(0,1).Value = 'Hello Excel'
##
##
##
##print ws.Cells(1,1).Value
##for i in range(1,5):
##    ws.Cells(i,2).Value = i  # Don't do this
####
##

