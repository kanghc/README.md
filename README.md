# README.md


<code> 
//Receiver
출처: http://swalloow.tistory.com/66 [MyCloud]
import java.io.*;
import java.net.*;
public class UDPFileReceiver extends Thread {
  final static int BUFSIZE=4096, port=4499;
  String fname=null;
  int fsize=0;
  DatagramSocket ds =null;
  DatagramPacket dp =null;
  byte[] buf = null;
  FileOutputStream fo=null;
  public static void main(String[] args){
	new UDPFileReceiver().start();
  }
  public void run(){
   try{
	 ds= new DatagramSocket(port);
    while(true){
	  buf= new byte[128];
	  dp= new DatagramPacket(buf, buf.length);
	  ds.receive(dp);
	  fname= new String(dp.getData()).trim();
	  fsize=Integer.parseInt(
		 fname.substring(fname.indexOf(":")+1));
	  fname= fname.substring(0,fname.indexOf(":"));
	  fo=new FileOutputStream(fname);
System.out.println(fname+", Total points ="+(fsize/BUFSIZE+1));
	  for(int i=0; i<fsize; i+=BUFSIZE){
	    buf= new byte[BUFSIZE];
	    dp= new DatagramPacket(buf, buf.length);
	    ds.receive(dp);
	    fo.write(dp.getData());
if(i%(BUFSIZE*80)==0) System.out.println();
System.out.print(".");
	  }
	  int extra = fsize%BUFSIZE;
	  buf= new byte[extra];
	  dp= new DatagramPacket(buf,buf.length);
	  ds.receive(dp);
	  fo.write(dp.getData());
	  fo.close();
System.out.println("\n"+fname+" is received.");
     }
   }catch(SocketException e){
   }catch(IOException ex){	   
   }
  }
}
</code>

<pre><code> 
//Sender 
import java.io.*;
import java.net.*;
public class UDPFileSender {
  public static void main(String[] a)throws Exception{

	int port=4499, BUFSIZE=4096;
	DatagramPacket dp=null;
	DatagramSocket ds= new DatagramSocket();
	BufferedReader br=new BufferedReader(new
						InputStreamReader(System.in));
	System.out.print("Server IP:");
	String ip = br.readLine();
	InetAddress ia = InetAddress.getByName(ip);
	System.out.print("File name:");
	String fname = br.readLine();
	File f = new File(fname);
	int fsize = (int) f.length();
	String mesg = fname+":"+fsize;
	FileInputStream fi = new FileInputStream(f);
	byte[] buf = mesg.getBytes();
	dp= new DatagramPacket(buf,buf.length,ia,port);
	ds.send(dp);
System.out.println("Total points ="+(fsize/BUFSIZE+1));
	for(int i=0; i<fsize; i+=BUFSIZE){
	  Thread.sleep(20);
	  buf= new byte[BUFSIZE];
	  fi.read(buf);
	  dp= new DatagramPacket(buf, buf.length,ia,port);
	  ds.send(dp);
if(i%(BUFSIZE*80)==0) System.out.println();
System.out.print(".");
	}
	int extra = fsize % BUFSIZE;
	buf = new byte[extra];
	fi.read(buf);
	dp= new DatagramPacket(buf, buf.length,ia,port);
	ds.send(dp);
	fi.close();
  }
}</code></pre>
