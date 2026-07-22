import socket
import json
import time
from rich.live import Live
from rich.table import Table

#============================
# DVL設定
#============================

HOST = "192.168.194.95"      # 修改成你的DVL IP
PORT = 16171                 # Water Linked DVL TCP Port
RECONNECT_TIMEOUT = 5        # 重連等待時間（秒）
MAX_RECONNECT_ATTEMPTS = 10  # 最大重連嘗試次數

#============================
# 儲存最新資料
#============================

dvl_data = {
    "vx": 0.0,
    "vy": 0.0,
    "vz": 0.0,
    "altitude": 0.0,
    "x": 0.0,
    "y": 0.0,
    "z": 0.0,
    "status": "Waiting..."
}


def connect_to_dvl(host, port):
    """建立與DVL的Socket連線"""
    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(10)  # 連線逾時設定為10秒
        sock.connect((host, port))
        print(f"✓ 成功連線到 {host}:{port}")
        return sock
    except socket.timeout:
        print(f"✗ 連線逾時: {host}:{port}")
        return None
    except ConnectionRefusedError:
        print(f"✗ 連線被拒絕: {host}:{port}")
        return None
    except Exception as e:
        print(f"✗ 連線失敗: {e}")
        return None


def reconnect_with_retry(host, port, max_attempts=MAX_RECONNECT_ATTEMPTS):
    """重連邏輯，帶有重試次數限制"""
    attempt = 0
    while attempt < max_attempts:
        attempt += 1
        print(f"\n[重連嘗試 {attempt}/{max_attempts}]")
        
        sock = connect_to_dvl(host, port)
        if sock is not None:
            return sock
        
        if attempt < max_attempts:
            print(f"等待 {RECONNECT_TIMEOUT} 秒後重試...")
            time.sleep(RECONNECT_TIMEOUT)
    
    print(f"✗ 無法在 {max_attempts} 次嘗試後連線")
    return None


def make_table():
    """生成顯示表格"""
    table = Table(title="Water Linked DVL A50")

    table.add_column("Parameter", justify="left")
    table.add_column("Value", justify="right")

    table.add_row("VX (m/s)", f"{dvl_data['vx']:.3f}")
    table.add_row("VY (m/s)", f"{dvl_data['vy']:.3f}")
    table.add_row("VZ (m/s)", f"{dvl_data['vz']:.3f}")

    table.add_row("Altitude (m)", f"{dvl_data['altitude']:.3f}")

    table.add_row("X (m)", f"{dvl_data['x']:.3f}")
    table.add_row("Y (m)", f"{dvl_data['y']:.3f}")
    table.add_row("Z (m)", f"{dvl_data['z']:.3f}")

    table.add_row("Status", dvl_data["status"])

    return table


def process_packet(packet):
    """處理接收到的JSON數據包"""
    try:
        #-------------------------
        # Velocity
        #-------------------------
        if packet.get("type") == "velocity":
            dvl_data["vx"] = packet.get("vx", 0)
            dvl_data["vy"] = packet.get("vy", 0)
            dvl_data["vz"] = packet.get("vz", 0)
            dvl_data["altitude"] = packet.get("altitude", 0)
            dvl_data["status"] = "Velocity"
            return True

        #-------------------------
        # Position
        #-------------------------
        elif packet.get("type") == "position_local":
            dvl_data["x"] = packet.get("x", 0)
            dvl_data["y"] = packet.get("y", 0)
            dvl_data["z"] = packet.get("z", 0)
            dvl_data["status"] = "Position"
            return True

    except KeyError:
        print(f"✗ 數據包缺少必要欄位: {packet}")
    except Exception as e:
        print(f"✗ 處理數據包出錯: {e}")
    
    return False


def main():
    """主程式"""
    sock = None
    buffer = ""

    try:
        # 初始連線
        sock = reconnect_with_retry(HOST, PORT)
        if sock is None:
            print("無法連線到DVL，程式結束")
            return

        with Live(make_table(), refresh_per_second=10) as live:
            while True:
                try:
                    # 接收數據
                    data = sock.recv(4096).decode()
                    
                    if not data:
                        # 空數據表示連線已斷開
                        print("\n✗ 連線已斷開")
                        dvl_data["status"] = "Connection Lost - Reconnecting..."
                        live.update(make_table())
                        
                        sock.close()
                        sock = reconnect_with_retry(HOST, PORT)
                        if sock is None:
                            break
                        buffer = ""
                        continue

                    buffer += data

                    # 逐行處理緩衝區數據
                    while "\n" in buffer:
                        line, buffer = buffer.split("\n", 1)
                        line = line.strip()
                        
                        if line:  # 忽略空行
                            try:
                                packet = json.loads(line)
                                process_packet(packet)
                            except json.JSONDecodeError:
                                print(f"✗ JSON解析失敗: {line}")
                            except Exception as e:
                                print(f"✗ 未預期的錯誤: {e}")

                    live.update(make_table())

                except socket.timeout:
                    print("\n✗ 接收數據逾時")
                    dvl_data["status"] = "Timeout - Reconnecting..."
                    live.update(make_table())
                    
                    sock.close()
                    sock = reconnect_with_retry(HOST, PORT)
                    if sock is None:
                        break
                    buffer = ""

                except ConnectionResetError:
                    print("\n✗ 連線被重置")
                    dvl_data["status"] = "Connection Reset - Reconnecting..."
                    live.update(make_table())
                    
                    sock.close()
                    sock = reconnect_with_retry(HOST, PORT)
                    if sock is None:
                        break
                    buffer = ""

                except Exception as e:
                    print(f"\n✗ 接收數據時出錯: {e}")
                    dvl_data["status"] = "Error - Reconnecting..."
                    live.update(make_table())
                    
                    sock.close()
                    sock = reconnect_with_retry(HOST, PORT)
                    if sock is None:
                        break
                    buffer = ""

    except KeyboardInterrupt:
        print("\n\n程式被用戶中斷")
    except Exception as e:
        print(f"\n✗ 主程式發生錯誤: {e}")
    finally:
        # 清理資源
        if sock is not None:
            sock.close()
            print("Socket已關閉")


if __name__ == "__main__":
    main()
