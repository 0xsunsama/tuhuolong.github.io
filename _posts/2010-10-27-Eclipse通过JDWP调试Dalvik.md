---
layout: post
title: Eclipse通过JDWP调试Dalvik
date: 2010-10-27 17:09:00
categories: [Java]
tags: [eclipse, command, classloader, methods, android]
---
Eclipse发送各种JDWP命令包,DalvikVM收到命令包(command package),响应并发送回复包(reply package),通过缓冲区交换数据
 
**JDWP:调试协议**
 
**(一)Android Dalvik实现的JDWP响应:**
static const JdwpHandlerMap gHandlerMap[] = {
    /* VirtualMachine command set (1) */
    { 1,    1,  handleVM_Version,       "VirtualMachine.Version" },
    { 1,    2,  handleVM_ClassesBySignature,
                                        "VirtualMachine.ClassesBySignature" },
    //1,    3,  VirtualMachine.AllClasses
    { 1,    4,  handleVM_AllThreads,    "VirtualMachine.AllThreads" },
    { 1,    5,  handleVM_TopLevelThreadGroups,
                                        "VirtualMachine.TopLevelThreadGroups" },
    { 1,    6,  handleVM_Dispose,       "VirtualMachine.Dispose" },
    { 1,    7,  handleVM_IDSizes,       "VirtualMachine.IDSizes" },
    { 1,    8,  handleVM_Suspend,       "VirtualMachine.Suspend" },
    { 1,    9,  handleVM_Resume,        "VirtualMachine.Resume" },
    { 1,    10, handleVM_Exit,          "VirtualMachine.Exit" },
    { 1,    11, handleVM_CreateString,  "VirtualMachine.CreateString" },
    { 1,    12, handleVM_Capabilities,  "VirtualMachine.Capabilities" },
    { 1,    13, handleVM_ClassPaths,    "VirtualMachine.ClassPaths" },
    { 1,    14, HandleVM_DisposeObjects, "VirtualMachine.DisposeObjects" },
    //1,    15, HoldEvents
    //1,    16, ReleaseEvents
    { 1,    17, handleVM_CapabilitiesNew,
                                        "VirtualMachine.CapabilitiesNew" },
    //1,    18, RedefineClasses
    //1,    19, SetDefaultStratum
    { 1,    20, handleVM_AllClassesWithGeneric,
                                        "VirtualMachine.AllClassesWithGeneric"},
    //1,    21, InstanceCounts

    /* ReferenceType command set (2) */
    { 2,    1,  handleRT_Signature,     "ReferenceType.Signature" },
    { 2,    2,  handleRT_ClassLoader,   "ReferenceType.ClassLoader" },
    { 2,    3,  handleRT_Modifiers,     "ReferenceType.Modifiers" },
    //2,    4,  Fields
    //2,    5,  Methods
    { 2,    6,  handleRT_GetValues,     "ReferenceType.GetValues" },
    { 2,    7,  handleRT_SourceFile,    "ReferenceType.SourceFile" },
    //2,    8,  NestedTypes
    { 2,    9,  handleRT_Status,        "ReferenceType.Status" },
    { 2,    10, handleRT_Interfaces,    "ReferenceType.Interfaces" },
    //2,    11, ClassObject
    { 2,    12, handleRT_SourceDebugExtension,
                                        "ReferenceType.SourceDebugExtension" },
    { 2,    13, handleRT_SignatureWithGeneric,
                                        "ReferenceType.SignatureWithGeneric" },
    { 2,    14, handleRT_FieldsWithGeneric,
                                        "ReferenceType.FieldsWithGeneric" },
    { 2,    15, handleRT_MethodsWithGeneric,
                                        "ReferenceType.MethodsWithGeneric" },
    //2,    16, Instances
    //2,    17, ClassFileVersion
    //2,    18, ConstantPool

    /* ClassType command set (3) */
    { 3,    1,  handleCT_Superclass,    "ClassType.Superclass" },
    { 3,    2,  handleCT_SetValues,     "ClassType.SetValues" },
    { 3,    3,  handleCT_InvokeMethod,  "ClassType.InvokeMethod" },
    //3,    4,  NewInstance

    /* ArrayType command set (4) */
    //4,    1,  NewInstance

    /* InterfaceType command set (5) */

    /* Method command set (6) */
    { 6,    1,  handleM_LineTable,      "Method.LineTable" },
    //6,    2,  VariableTable
    //6,    3,  Bytecodes
    //6,    4,  IsObsolete
    { 6,    5,  handleM_VariableTableWithGeneric,
                                        "Method.VariableTableWithGeneric" },

    /* Field command set (8) */

    /* ObjectReference command set (9) */
    { 9,    1,  handleOR_ReferenceType, "ObjectReference.ReferenceType" },
    { 9,    2,  handleOR_GetValues,     "ObjectReference.GetValues" },
    { 9,    3,  handleOR_SetValues,     "ObjectReference.SetValues" },
    //9,    4,  (not defined)
    //9,    5,  MonitorInfo
    { 9,    6,  handleOR_InvokeMethod,  "ObjectReference.InvokeMethod" },
    { 9,    7,  handleOR_DisableCollection,
                                        "ObjectReference.DisableCollection" },
    { 9,    8,  handleOR_EnableCollection,
                                        "ObjectReference.EnableCollection" },
    { 9,    9,  handleOR_IsCollected,   "ObjectReference.IsCollected" },
    //9,    10, ReferringObjects

    /* StringReference command set (10) */
    { 10,   1,  handleSR_Value,         "StringReference.Value" },

    /* ThreadReference command set (11) */
    { 11,   1,  handleTR_Name,          "ThreadReference.Name" },
    { 11,   2,  handleTR_Suspend,       "ThreadReference.Suspend" },
    { 11,   3,  handleTR_Resume,        "ThreadReference.Resume" },
    { 11,   4,  handleTR_Status,        "ThreadReference.Status" },
    { 11,   5,  handleTR_ThreadGroup,   "ThreadReference.ThreadGroup" },
    { 11,   6,  handleTR_Frames,        "ThreadReference.Frames" },
    { 11,   7,  handleTR_FrameCount,    "ThreadReference.FrameCount" },
    //11,   8,  OwnedMonitors
    { 11,   9,  handleTR_CurrentContendedMonitor,
                                    "ThreadReference.CurrentContendedMonitor" },
    //11,   10, Stop
    //11,   11, Interrupt
    { 11,   12, handleTR_SuspendCount,  "ThreadReference.SuspendCount" },
    //11,   13, OwnedMonitorsStackDepthInfo
    //11,   14, ForceEarlyReturn

    /* ThreadGroupReference command set (12) */
    { 12,   1,  handleTGR_Name,         "ThreadGroupReference.Name" },
    { 12,   2,  handleTGR_Parent,       "ThreadGroupReference.Parent" },
    { 12,   3,  handleTGR_Children,     "ThreadGroupReference.Children" },

    /* ArrayReference command set (13) */
    { 13,   1,  handleAR_Length,        "ArrayReference.Length" },
    { 13,   2,  handleAR_GetValues,     "ArrayReference.GetValues" },
    { 13,   3,  handleAR_SetValues,     "ArrayReference.SetValues" },

    /* ClassLoaderReference command set (14) */
    { 14,   1,  handleCLR_VisibleClasses,
                                        "ClassLoaderReference.VisibleClasses" },

    /* EventRequest command set (15) */
    { 15,   1,  handleER_Set,           "EventRequest.Set" },
    { 15,   2,  handleER_Clear,         "EventRequest.Clear" },
    //15,   3,  ClearAllBreakpoints

    /* StackFrame command set (16) */
    { 16,   1,  handleSF_GetValues,     "StackFrame.GetValues" },
    { 16,   2,  handleSF_SetValues,     "StackFrame.SetValues" },
    { 16,   3,  handleSF_ThisObject,    "StackFrame.ThisObject" },
    //16,   4,  PopFrames

    /* ClassObjectReference command set (17) */
    { 17,   1,  handleCOR_ReflectedType,"ClassObjectReference.ReflectedType" },

    /* Event command set (64) */
    //64,  100, Composite   <-- sent from VM to debugger, never received by VM

    { 199,  1,  handleDDM_Chunk,        "DDM.Chunk" },
};
 
**(二)具体的请求和响应:**
Eclipse进入debug状态
(1)发送JDWP的ReferenceType.FieldsWithGeneric命令包, dalvik响应调用handleRT_FieldsWithGeneric->dvmDbgOutputAllFields将某个类的所有域信息放入缓冲,Eclipse读取以显示
(2)发送JDWP的ObjectReference.GetValues命令包,dalvik响应调用handleOR_GetValues->dvmDbgGetFieldValue将域值放入缓冲区
