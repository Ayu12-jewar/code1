import os
import platform
import socket
import subprocess
import re
import uuid
import psutil
import speedtest
import platform
import gpuinfo

def get_installed_software():
    if platform.system() == "Windows":
        installed_software = subprocess.check_output(['wmic', 'product', 'get', 'name']).decode('utf-8')
        return re.findall(r'\bName\b\s*([^\r\n]*)', installed_software)
    elif platform.system() == "Linux":
        # Linux-specific code
        installed_software = subprocess.check_output(['dpkg', '--get-selections']).decode('utf-8')
        return re.findall(r'(\S+)(?:\s+install)', installed_software)
    else:
        return []



def get_screen_resolution():
    if platform.system() == "Windows":
        import ctypes
        user32 = ctypes.windll.user32
        return user32.GetSystemMetrics(0), user32.GetSystemMetrics(1)
    elif platform.system() == "Linux":
        # Linux-specific code
        return "Not implemented without psutil"
    else:
        return "Not implemented for this platform"

def get_cpu_info():
    if platform.system() == "Windows":
        cpu_info = subprocess.check_output(['wmic', 'cpu', 'get', 'caption']).decode('utf-8')
        return re.search(r'\bCaption\b\s*([^\r\n]*)', cpu_info).group(1)
    elif platform.system() == "Linux":
        # Linux-specific code
        return "Not implemented without psutil"
    else:
        return "Not implemented for this platform"
    
    
def get_cpu_cores_threads():
    if platform.system() == "Windows":
        import multiprocessing
        return multiprocessing.cpu_count()
    elif platform.system() == "Linux":
        # Linux-specific code
        return "Not implemented without external libraries"
    else:
        return "Not implemented for this platform"



def get_ram_size():
    ram_info = subprocess.check_output(['wmic', 'memorychip', 'get', 'capacity']).decode('utf-8')
    ram_size_bytes = sum(int(capacity) for capacity in re.findall(r'\bCapacity\b\s*([^\r\n]*)', ram_info))
    ram_size_gb = ram_size_bytes / (1024 ** 3)
    return ram_size_gb



def get_mac_address():
    mac_address = ':'.join(re.findall('..', '%012x' % uuid.getnode()))
    return mac_address

def get_public_ip_address():
    public_ip = socket.gethostbyname(socket.gethostname())
    return public_ip

def get_windows_version():
    windows_version = platform.system() + " " + platform.version()
    return windows_version





def get_gpu_model():
    try:
        gpu_info = gpuinfo.get_info()
        if gpu_info:
            return gpu_info[0]['Name']
        else:
            return "No GPU detected"
    except Exception as e:
        return f"Error fetching GPU info: {str(e)}"

def get_screen_size():
    try:
        system_info = platform.system()
        if system_info == "Windows":
            import ctypes
            user32 = ctypes.windll.user32
            width = user32.GetSystemMetrics(0)
            height = user32.GetSystemMetrics(1)
            return f"{width}x{height} pixels"
        elif system_info == "Linux" or system_info == "Darwin":
            import subprocess
            command = "xrandr | grep '*' | awk '{print $1}'"
            screen_size = subprocess.check_output(command, shell=True).decode("utf-8").strip()
            return screen_size
        else:
            return "Screen size retrieval not supported on this platform"
    except Exception as e:
        return f"Error fetching screen size: {str(e)}"

# Example usage
gpu_model = get_gpu_model()
screen_size = get_screen_size()





def get_internet_speed():
    st = speedtest.Speedtest()
    download_speed = st.download() / 10**6  # Convert to Mbps
    upload_speed = st.upload() / 10**6  # Convert to Mbps
    return f"Download Speed: {download_speed:.2f} Mbps, Upload Speed: {upload_speed:.2f} Mbps"



# Displaying the information
print("Number of CPU Cores and Threads:", get_cpu_cores_threads())
print(f"GPU Model: {gpu_model}")
print(f"Screen Size: {screen_size}")
print("RAM Size (GB):", get_ram_size())
print("MAC Address:", get_mac_address())
print("Public IP Address:", get_public_ip_address())
print("Windows Version:", get_windows_version())
print("Installed Software:", get_installed_software())
print("Internet Speed:", get_internet_speed())
print("Screen Resolution:", get_screen_resolution())
print("CPU Model:", get_cpu_info())
# Add more functions for other details
