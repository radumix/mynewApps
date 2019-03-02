# mynewApps
mynewApps

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using System.Diagnostics;
using System.IO;
using System.Reflection;

namespace BlockInternet
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            //ADDED A REFERENCE TO SYSTEM.SERVICEPROCESS VIA PROJECT->ADD REFERENCE, .NET tab
            System.ServiceProcess.ServiceController scPAServ = new System.ServiceProcess.ServiceController("PolicyAgent"); //IPSec

            if (scPAServ.Status != System.ServiceProcess.ServiceControllerStatus.Running)
            {
                scPAServ.Start(); //Start If Not Running
            }
            string[] strCommands =  { @"-w REG -p ""Firewall"" -r ""Block All"" -f *:*:*+*:*:* -n BLOCK -x" ,
                            @"-w REG -p ""Firewall"" -r ""Allow LAN"" -f 0:*:*+192.168.10.*:*:* -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""DNS"" -f 0:*:UDP+223.211.190.23:53:UDP 0:*:UDP+223.211.190.24:53:UDP 0:*:TCP+223.211.190.23:53:TCP 0:*:TCP+223.211.190.24:53:TCP -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""POP3"" -f 0:*:TCP+*:110:TCP -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""POP3S"" -f 0:*:TCP+*:995:TCP -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""FTP Control"" -f 0:*:TCP+*:21:TCP -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""FTP Data"" -f 0:*:TCP+*:20:TCP -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""IMAP"" -f 0:*:TCP+*:143:TCP -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""HTTP"" -f 0:*:TCP+*:80:TCP -n PASS -x" ,
                            @"-w REG -p ""Firewall"" -r ""HTTPS"" -f 0:*:TCP+*:443:TCP -n BLOCK -x" ,
                            @"-w REG -p ""Firewall"" -r ""PROXY"" -f 0:*:TCP+*:8080:TCP 0:*:TCP+*:3128:TCP 0:*:TCP+*:8081:*:TCP 0:*:TCP+*:8000:TCP -n BLOCK -x"};

            for (int i = 0; i < strCommands.Length; i++) //Loop through each Command String
            {
                ProcessStartInfo psiStart = new ProcessStartInfo(); //Process To Start

                psiStart.CreateNoWindow = true; //Invisible

                psiStart.FileName = Directory.GetParent(Assembly.GetExecutingAssembly().Location) + "\\ipseccmd.exe"; //IPSEC

                psiStart.Arguments = strCommands[i]; //Break Command Strings Apart

                psiStart.WindowStyle = ProcessWindowStyle.Hidden; //Invisible

                Process p = System.Diagnostics.Process.Start(psiStart); //Start Process To Block Internet Connection
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {
            System.ServiceProcess.ServiceController scPAServ = new System.ServiceProcess.ServiceController("PolicyAgent"); //IPSec


            if (scPAServ.Status != System.ServiceProcess.ServiceControllerStatus.Running) //If Not Running
            {
                scPAServ.Start(); 
            }
            string strCommands = @"-w REG -p ""Firewall"" -r ""Block All"" -f *:*:*+*:*:* -n BLOCK -y"; //Commands To Send

            ProcessStartInfo psiStart = new ProcessStartInfo(); //Process To Start

            psiStart.CreateNoWindow = true; //Invisible

            psiStart.FileName = Directory.GetParent(Assembly.GetExecutingAssembly().Location) + "\\ipseccmd.exe"; //IPSEC

            psiStart.Arguments = strCommands; //Give Command String As Argument

            psiStart.WindowStyle = ProcessWindowStyle.Hidden;  //Invisible

            Process p = System.Diagnostics.Process.Start(psiStart);  //Start Process To Stop Internet Connection
        }
    }
}
