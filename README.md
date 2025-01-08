# PostCARD: HyperLogLog Integration for PostgreSQL Join Cardinality Estimation

PostCARD (PostgreSQL Cardinality Estimation using HyperLogLog) is an extension to PostgreSQL that implements efficient join cardinality estimation using HyperLogLog sketches. It provides accurate cardinality estimates while maintaining minimal memory overhead.

## Features

- Memory Efficient: Uses only `64KB` per HLL sketch
- High Accuracy: Consistently achieves error rates below `0.5%`
- Scalable: Successfully tested with datasets ranging from 1K to 500K rows
- Native Integration: Seamlessly integrates with PostgreSQL's query planner
- Custom Keyword: Introduces HLL_JOIN for explicit HyperLogLog-based estimation

## How It Works

PostCARD implements cardinality estimation using the HyperLogLog algorithm with the following key components:

1. **HyperLogLog Sketches**: Uses probabilistic data structures to track unique values
2. **Inclusion-Exclusion Principle**: Calculates intersection cardinalities
3. **16-bit Precision**: Balances accuracy and memory usage
4. **Bias Correction**: Implements standard error correction for improved accuracy

The standard HLL cardinality estimate is calculated using:

```
E = αm · m² · (Σ(2^-M[j]))^-1
```
Where:
- m = Number of registers (2^16)
- αm = Bias correction constant
- M[j] = Maximum leading zeros in register j

## Performance Results

| Join Category | Actual Count | Estimated Count | Error Percentage |
|--------------|--------------|-----------------|------------------|
| Tiny-Small Join (1K-50K) | 1,000 | 1,001 | ~0.1% |
| Small-Medium Join (50K-250K) | 50,000 | 49,937 | ~0.126% |
| Medium-Large Join (250K-500K) | 250,000 | 250,367 | ~0.147% |
| Large-Large Self Join (500K-500K) | 500,000 | 498,335 | ~0.333% |


## Installation

1. Clone the repository:
```bash
git clone https://github.com/LightningNemesis/PostCARD.git
```

2. Build the system:
```bash
cd PostCARD
make install
```

## Usage

Basic usage example:
```sql
SELECT * FROM table1 HLL_JOIN table2 ON table1.id = table2.id;
```


## Architecture
The implementation consists of three main components:

1. Parser Integration
	- Added HLL_JOIN keyword
	- Extended join type enumeration
	- Created JoinExpr node for HLL operations

2. Planner Modifications
	- Implemented HLL-specific join paths
	- Integrated cardinality estimation logic
	- Enhanced path selection algorithms

3. Executor Enhancements
	- Added HLL sketch initialization
	- Implemented sketch population logic
	- Created merge operations for sketches


## Technical Details

### Memory Usage
- Each HLL sketch uses 2^16 (65,536) registers
- 1 byte per register
- Total memory per sketch: 64KB
- Standard error: 1.04/√65536 ≈ 0.004 (0.4%)

### Precision Levels

- Low (10 bits): 1KB memory, ~3.2% error
- Medium (13 bits): 8KB memory, ~1.1% error
- High (16 bits): 64KB memory, ~0.4% error

## Authors

- Abhishek Kumar (akumar17@usc.edu)
- Parinda Pranami (pranami@usc.edu)
- Rahul Tangsali (tangsali@usc.edu)

## Project Context
This project was developed as part of the *Advanced Data Stores* course at the **University of Southern California** led by *Professor Sabek Ibrahim*.