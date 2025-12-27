# About Ethernet cable

### CAT1 

- **Use Case**: **Analog telephone lines** and early **ISDN (Integrated Services Digital Network)** lines.
- **Characteristics**: CAT1 cables were basic twisted-pair cables without specific shielding, used solely for **voice communication**. They were never certified for data transmission and could not handle Ethernet or network data.
- **Example in Use**: In older office buildings or homes, CAT1 cables connected telephones and fax machines. These cables would not have the same RJ45 connectors that are standard for Ethernet but rather simple connectors suited for telephone jacks (e.g., RJ11).

### CAT2 

- **Use Case**: Early **4 Mbps Token Ring** networks developed by IBM.
- **Characteristics**: CAT2 cables were also twisted pair cables, but they had slightly better specifications than CAT1, allowing them to handle **4 Mbps data** rates over short distances (typically within a building).
- **Example in Use**: CAT2 was commonly used in the early 1980s and early 1990s for **Token Ring networks**, which were popular in IBM business environments before Ethernet became the standard.



## some more info about these two

**CAT1** is essentially a **telephone cable** used primarily for analog voice communication, such as landline telephones and older systems like fax machines. It’s not designed for data transmission and lacks the specifications required for Ethernet or networking applications.

**CAT2** can also be thought of as a type of **telephone cable** but with slight improvements, making it suitable for early, low-speed data networks, such as IBM’s 4 Mbps Token Ring. However, it’s still very limited by today’s standards and was primarily used in older business environments.

### Why They’re Rarely Seen Now

Neither CAT1 nor CAT2 cables are used in modern network infrastructures, as they do not support Ethernet or fast data speeds. The **CAT3** standard, which supports 10 Mbps, marked the real beginning of Ethernet-capable cabling and quickly became the minimum for data applications.

### CAT3 and Up

### CAT3 (Category 3)

- **Introduction**: 1980s
- **Max Data Rate**: 10 Mbps (10BASE-T Ethernet)
- **Max Bandwidth**: 16 MHz
- **Max Distance**: Up to 100 meters (328 feet) for 10 Mbps
- **Use Cases**: Primarily used in early Ethernet networks (10BASE-T) and telephone wiring.
- **Characteristics**: Unshielded twisted pair (UTP) with less twist than higher categories, which limits performance and interference reduction. Mostly obsolete for modern data networks but still sometimes used in voice applications.

### CAT4 (Category 4)

- **Introduction**: Early 1990s
- **Max Data Rate**: 16 Mbps (Token Ring networks)
- **Max Bandwidth**: 20 MHz
- **Max Distance**: Up to 100 meters
- **Use Cases**: Mostly used in **Token Ring** networks (an IBM networking standard) before Ethernet became dominant.
- **Characteristics**: Slightly better interference resistance than CAT3, but rarely used anymore and almost completely phased out.

### CAT5 (Category 5)

- **Introduction**: Mid-1990s
- **Max Data Rate**: 100 Mbps (100BASE-TX Ethernet)
- **Max Bandwidth**: 100 MHz
- **Max Distance**: Up to 100 meters for 100 Mbps
- **Use Cases**: Widely used in early Ethernet networks (100BASE-T) but phased out in favor of CAT5e.
- **Characteristics**: Improved twist rate over CAT3 and CAT4, which helps reduce crosstalk and interference. Often UTP and unshielded. Officially obsolete today.

### CAT5e (Category 5e, “Enhanced”)

- **Introduction**: 1999
- **Max Data Rate**: 1 Gbps (1000BASE-T Gigabit Ethernet)
- **Max Bandwidth**: 100 MHz
- **Max Distance**: Up to 100 meters for Gigabit Ethernet
- **Use Cases**: Common in home and office networks for Gigabit Ethernet. Still widely used and suitable for most standard applications.
- **Characteristics**: More stringent specifications for crosstalk and EMI (electromagnetic interference) compared to CAT5. UTP is common, though shielded options (STP) are available for high-interference areas.

### CAT6 (Category 6)

- **Introduction**: Early 2000s
- **Max Data Rate**: 1 Gbps (1000BASE-T) at up to 100 meters, and 10 Gbps (10GBASE-T) at up to 55 meters
- **Max Bandwidth**: 250 MHz
- **Max Distance**: 100 meters for 1 Gbps, 55 meters for 10 Gbps
- **Use Cases**: Suitable for high-speed home networks, data centers, and enterprise applications.
- **Characteristics**: Tighter twists and often includes a physical separator (or spline) to reduce crosstalk and EMI. Comes in both UTP and STP forms. More durable and with better performance than CAT5e.

### CAT6A (Category 6A, “Augmented”)

- **Introduction**: Mid-2000s
- **Max Data Rate**: 10 Gbps (10GBASE-T) at 100 meters
- **Max Bandwidth**: 500 MHz
- **Max Distance**: 100 meters for 10 Gbps
- **Use Cases**: Data centers, high-performance networks, and future-proofing high-speed installations.
- **Characteristics**: Improved shielding and insulation to reduce alien crosstalk (interference from nearby cables). CAT6A is usually thicker and less flexible than CAT6 due to extra shielding, often found in STP form for enhanced protection.

### CAT7 (Category 7)

- **Introduction**: 2010s
- **Max Data Rate**: 10 Gbps
- **Max Bandwidth**: 600 MHz
- **Max Distance**: 100 meters for 10 Gbps
- **Use Cases**: Data centers, structured cabling in commercial buildings, often in applications requiring strict interference control.
- **Characteristics**: Fully shielded twisted pairs (S/FTP or F/FTP) and a unique GG45 or TERA connector (backward-compatible with RJ45 in some cases). Offers robust protection against crosstalk and external interference, making it durable in high-interference areas.

### CAT8 (Category 8)

- **Introduction**: 2016
- **Max Data Rate**: 25 Gbps to 40 Gbps (typically used in data centers)
- **Max Bandwidth**: 2000 MHz
- **Max Distance**: 30 meters for 40 Gbps
- **Use Cases**: Primarily for data centers and professional environments where ultra-high-speed short-range connections are necessary.
- **Characteristics**: Fully shielded with S/FTP construction to handle high frequencies and reduce interference. CAT8 cables usually use standard RJ45 connectors but are designed for use with high-end switches, routers, and networking hardware.

### Summary Table

| Category | Max Data Rate    | Max Bandwidth | Max Distance                 | Typical Use                          |
| -------- | ---------------- | ------------- | ---------------------------- | ------------------------------------ |
| CAT3     | 10 Mbps          | 16 MHz        | 100 meters                   | Early Ethernet, telephone            |
| CAT4     | 16 Mbps          | 20 MHz        | 100 meters                   | Token Ring                           |
| CAT5     | 100 Mbps         | 100 MHz       | 100 meters                   | Early Ethernet (obsolete)            |
| CAT5e    | 1 Gbps           | 100 MHz       | 100 meters                   | Standard home/office use             |
| CAT6     | 1 Gbps / 10 Gbps | 250 MHz       | 100m (1 Gbps), 55m (10 Gbps) | High-speed home/office               |
| CAT6A    | 10 Gbps          | 500 MHz       | 100 meters                   | Data centers, future-proof           |
| CAT7     | 10 Gbps          | 600 MHz       | 100 meters                   | Structured cabling, commercial use   |
| CAT8     | 25-40 Gbps       | 2000 MHz      | 30 meters                    | Data centers, short-range high speed |