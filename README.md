# SUMMER_INTERNSHIP_TASK3

LIVE VIDEO STREAMING using OPEN-CV and SOCKET_PROGRAMMING
Adithya Gangadhar Shetty
Adithya Gangadhar Shetty

Just now·5 min read





Before we get into the “HOW TO PERFORM LIVE VIDEO STREAMING “ lets get basic knowledge of the modules use for it
OpenCV
OpenCV was started at Intel in 1999 by Gary Bradsky, and the first release came out in 2000. Vadim Pisarevsky joined Gary Bradsky to manage Intel’s Russian software OpenCV team. In 2005, OpenCV was used on Stanley, the vehicle that won the 2005 DARPA Grand Challenge. Later, its active development continued under the support of Willow Garage with Gary Bradsky and Vadim Pisarevsky leading the project. OpenCV now supports a multitude of algorithms related to Computer Vision and Machine Learning and is expanding day by day.
OpenCV supports a wide variety of programming languages such as C++, Python, Java, etc., and is available on different platforms including Windows, Linux, OS X, Android, and iOS. Interfaces for high-speed GPU operations based on CUDA and OpenCL are also under active development.
OpenCV-Python is the Python API for OpenCV, combining the best qualities of the OpenCV C++ API and the Python language.
OpenCV-Python
OpenCV-Python is a library of Python bindings designed to solve computer vision problems.
Compared to languages like C/C++, Python is slower. That said, Python can be easily extended with C/C++, which allows us to write computationally intensive code in C/C++ and create Python wrappers that can be used as Python modules. This gives us two advantages: first, the code is as fast as the original C/C++ code (since it is the actual C++ code working in background) and second, it easier to code in Python than C/C++. OpenCV-Python is a Python wrapper for the original OpenCV C++ implementation.
OpenCV-Python makes use of Numpy, which is a highly optimized library for numerical operations with a MATLAB-style syntax. All the OpenCV array structures are converted to and from Numpy arrays. This also makes it easier to integrate with other libraries that use Numpy such as SciPy and Matplotlib.
OpenCV-Python is a library of Python bindings designed to solve computer vision problems.
cv2
cv2.imread() method loads an image from the specified file. If the image cannot be read (because of missing file, improper permissions, unsupported or invalid format) then this method returns an empty matrix.
Syntax: cv2.imread(path, flag)
Parameters:
path: A string representing the path of the image to be read.
flag: It specifies the way in which image should be read. It’s default value is cv2.IMREAD_COLOR
Return Value: This method returns an image that is loaded from the specified file.
What is socket programming

Socket programming is a way of connecting two nodes on network to communicate with each other. One socket(node) listens on a particular port at an IP, while other socket reaches out to the other to form a connection. Server forms the listener socket while client reaches out to the server.

NOW LETS BEGIN WITH TASK!!!!
Here i will be creating live video streaming app with the help of OPEN-CV and SOCKET PROGRAMMING
lets implement code for Streamer
SENDER.py
import socket,pickle,struct
import cv2
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)  #TCP connection
print("Enter Your IP")
ip=input()
print("Enter Your port")
port=int(input())
print("Enter Your Partner IP")
pip=input()
print("Enter Your Partner port")
pport=int(input())
s.bind((ip,port))
s.listen(5)  #5 is backlog meaning it can accept 5 connections at a time
cap = cv2.VideoCapture(0)
while True:
    client_socket,addr = s.accept()
    print('GOT CONNECTION FROM:',addr)
    if client_socket:
        vid = cv2.VideoCapture(0)
        
        while(vid.isOpened()):
            img,frame = vid.read()
            a = pickle.dumps(frame)   #serialize frame to byte data
            message = struct.pack("Q",len(a))+a #pack each frame #Q is 8 bytes
            client_socket.sendall(message)
            
            cv2.imshow('TRANSMITTING VIDEO',frame)
            key = cv2.waitKey(1) & 0xFF
            if key ==ord('q'):     #press q to exit
                client_socket.close()
RECEIVER.py
import socket
import cv2,pickle,struct
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)   #TCP connection
print("ENter your IP")
ip=input()
print("ENter your PORT")
port=int(input())
s.bind((ip,port))
print("ENter your streamer's IP")
ip1=input()
print("ENter streamer's PORT")
port1=int(input())
s.connect((ip1,port1))
cap=cv2.VideoCapture(0)
data = b""
payload_size = struct.calcsize("Q")   #8 bytes
while True:
    while len(data) < payload_size:
        packet = s.recv(4*1024) #4k buffersize
        if not packet: break    #if no data, then break
        data+=packet            #if data comes,append to data until buffer full
    packed_msg_size = data[:payload_size]   #first 8 bytes have packed msg
    data = data[payload_size:]   #after initial 8 bytes, frame data present
    msg_size = struct.unpack("Q",packed_msg_size)[0] #unpacking packed message and getting 
    
    while len(data) < msg_size:            
        data += s.recv(4*1024)
    frame_data = data[:msg_size]
    data  = data[msg_size:]
    frame = pickle.loads(frame_data)
    cv2.imshow("RECEIVING VIDEO",frame)
    key = cv2.waitKey(1) & 0xFF
    if key  == ord('q'):
        break
s.close()
Here, we are using two extra libraries to serialize the video data frames and pack the data to send to client.
“Pickling” is the process whereby a Python object hierarchy is converted into a byte stream, and “unpickling” is the inverse operation, whereby a byte stream (from a binary file or bytes-like object) is converted back into an object hierarchy.
The struct module in Python is used to convert native Python data types such as strings and numbers into a string of bytes and vice versa.It is used mostly for handling binary data stored in files or from network connections, among other sources.
struct.pack()
Syntax: 
struct.pack(format, v1, v2, ...)
Syntax: 
struct.pack(format, v1, v2, ...)
Return a string containing the values v1, v2, … , that are packed according to the given format (Format strings are the mechanism used to specify the expected layout when packing and unpacking data).The values followed by the format must be as per the format only, else struct.error is raised.
When an application puts a socket into LISTEN state using the listen syscall, it needs to specify a backlog for that socket. The backlog is usually described as the limit for the queue of incoming connections.
OUTPUT
Here are the results after successfull execution of the CODE


Thanks for reading !!!
