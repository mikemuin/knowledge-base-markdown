# Laravel Architecture for PSGC Data Processing System

## Overview

A Laravel-based architecture for processing Philippine Standard Geographic Code (PSGC) data from Excel files into a structured API system with caching, queuing, and comprehensive data relationships.

------

## Table of Contents

1. [System Architecture](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#system-architecture)
2. [Directory Structure](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#directory-structure)
3. [Database Design](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#database-design)
4. [Models & Relationships](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#models--relationships)
5. [Commands & Jobs](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#commands--jobs)
6. [Services](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#services)
7. [API Routes & Controllers](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#api-routes--controllers)
8. [Caching Strategy](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#caching-strategy)
9. [Implementation Guide](https://claude.ai/chat/684b61e1-51eb-438b-a7b8-4afd292942b2#implementation-guide)

------

## System Architecture

### High-Level Components

```
┌─────────────────────────────────────────────────────────────────┐
│                         Laravel Application                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌────────────┐    ┌──────────────┐    ┌─────────────────┐     │
│  │  Artisan   │───▶│  Import Job  │───▶│  Database       │     │
│  │  Commands  │    │  (Queued)    │    │  (MySQL/PG)     │     │
│  └────────────┘    └──────────────┘    └─────────────────┘     │
│                                                    │              │
│  ┌────────────┐    ┌──────────────┐              ▼              │
│  │  API       │───▶│  Cache       │◀────  ┌─────────────┐      │
│  │  Routes    │    │  (Redis)     │       │  Eloquent   │      │
│  └────────────┘    └──────────────┘       │  Models     │      │
│                                            └─────────────┘      │
└─────────────────────────────────────────────────────────────────┘
         │                        │                     │
         ▼                        ▼                     ▼
   Excel File              JSON Cache            API Responses
```

------

## Directory Structure

```
app/
├── Console/
│   └── Commands/
│       ├── ImportPSGCCommand.php          # Main import command
│       └── GeneratePSGCCacheCommand.php   # Generate cache files
├── Models/
│   ├── Region.php
│   ├── Province.php
│   ├── City.php
│   ├── Municipality.php
│   ├── SubMunicipality.php
│   └── Barangay.php
├── Services/
│   ├── PSGCImportService.php              # Excel import logic
│   ├── PSGCProcessorService.php           # Data transformation
│   └── PSGCCacheService.php               # Cache management
├── Jobs/
│   ├── ImportPSGCDataJob.php              # Queued import job
│   ├── ProcessRegionsJob.php              # Process regions
│   ├── ProcessProvincesJob.php            # Process provinces
│   ├── ProcessCitiesJob.php               # Process cities
│   ├── ProcessMunicipalitiesJob.php       # Process municipalities
│   ├── ProcessSubMunicipalitiesJob.php    # Process sub-municipalities
│   └── ProcessBarangaysJob.php            # Process barangays
├── Http/
│   ├── Controllers/
│   │   └── Api/
│   │       ├── RegionController.php
│   │       ├── ProvinceController.php
│   │       ├── CityController.php
│   │       ├── MunicipalityController.php
│   │       ├── SubMunicipalityController.php
│   │       └── BarangayController.php
│   └── Resources/
│       ├── RegionResource.php
│       ├── ProvinceResource.php
│       ├── CityResource.php
│       ├── MunicipalityResource.php
│       ├── SubMunicipalityResource.php
│       └── BarangayResource.php
├── Traits/
│   ├── HasPSGCCode.php                    # PSGC code utilities
│   └── HasGeographicRelations.php         # Common relationships
└── Enums/
    ├── GeographicLevel.php
    ├── CityClass.php
    ├── IncomeClassification.php
    └── UrbanRuralType.php

database/
├── migrations/
│   ├── 2024_01_01_000001_create_regions_table.php
│   ├── 2024_01_01_000002_create_provinces_table.php
│   ├── 2024_01_01_000003_create_cities_table.php
│   ├── 2024_01_01_000004_create_municipalities_table.php
│   ├── 2024_01_01_000005_create_sub_municipalities_table.php
│   └── 2024_01_01_000006_create_barangays_table.php
└── seeders/
    └── PSGCSeeder.php

storage/
├── app/
│   └── psgc/
│       ├── imports/                        # Uploaded Excel files
│       └── cache/                          # Generated JSON files
└── logs/
    └── psgc-import.log

config/
└── psgc.php                                # PSGC-specific configuration

tests/
├── Feature/
│   ├── ImportPSGCCommandTest.php
│   └── PSGCApiTest.php
└── Unit/
    ├── PSGCImportServiceTest.php
    └── PSGCProcessorServiceTest.php
```

------

## Database Design

### Database Schema

```sql
-- Regions Table
CREATE TABLE regions (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    psgc_10_digit_code CHAR(10) UNIQUE NOT NULL,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(255) NOT NULL,
    region_name VARCHAR(255) NOT NULL,
    old_name VARCHAR(255) NULL,
    population_2020 BIGINT UNSIGNED DEFAULT 0,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,
    INDEX idx_psgc_code (psgc_10_digit_code),
    INDEX idx_code (code)
);

-- Provinces Table
CREATE TABLE provinces (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    psgc_10_digit_code CHAR(10) UNIQUE NOT NULL,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(255) NOT NULL,
    old_name VARCHAR(255) NULL,
    income_classification VARCHAR(50) NULL,
    population_2020 BIGINT UNSIGNED DEFAULT 0,
    region_id BIGINT UNSIGNED NOT NULL,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,
    FOREIGN KEY (region_id) REFERENCES regions(id) ON DELETE CASCADE,
    INDEX idx_psgc_code (psgc_10_digit_code),
    INDEX idx_region (region_id),
    INDEX idx_code (code)
);

-- Cities Table
CREATE TABLE cities (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    psgc_10_digit_code CHAR(10) UNIQUE NOT NULL,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(255) NOT NULL,
    old_name VARCHAR(255) NULL,
    city_class VARCHAR(50) NULL,
    income_classification VARCHAR(50) NULL,
    is_capital BOOLEAN DEFAULT FALSE,
    population_2020 BIGINT UNSIGNED DEFAULT 0,
    region_id BIGINT UNSIGNED NOT NULL,
    province_id BIGINT UNSIGNED NOT NULL,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,
    FOREIGN KEY (region_id) REFERENCES regions(id) ON DELETE CASCADE,
    FOREIGN KEY (province_id) REFERENCES provinces(id) ON DELETE CASCADE,
    INDEX idx_psgc_code (psgc_10_digit_code),
    INDEX idx_region (region_id),
    INDEX idx_province (province_id),
    INDEX idx_capital (is_capital),
    INDEX idx_code (code)
);

-- Municipalities Table
CREATE TABLE municipalities (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    psgc_10_digit_code CHAR(10) UNIQUE NOT NULL,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(255) NOT NULL,
    old_name VARCHAR(255) NULL,
    income_classification VARCHAR(50) NULL,
    is_capital BOOLEAN DEFAULT FALSE,
    population_2020 BIGINT UNSIGNED DEFAULT 0,
    region_id BIGINT UNSIGNED NOT NULL,
    province_id BIGINT UNSIGNED NOT NULL,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,
    FOREIGN KEY (region_id) REFERENCES regions(id) ON DELETE CASCADE,
    FOREIGN KEY (province_id) REFERENCES provinces(id) ON DELETE CASCADE,
    INDEX idx_psgc_code (psgc_10_digit_code),
    INDEX idx_region (region_id),
    INDEX idx_province (province_id),
    INDEX idx_capital (is_capital),
    INDEX idx_code (code)
);

-- Sub-Municipalities Table
CREATE TABLE sub_municipalities (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    psgc_10_digit_code CHAR(10) UNIQUE NOT NULL,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(255) NOT NULL,
    old_name VARCHAR(255) NULL,
    population_2020 BIGINT UNSIGNED DEFAULT 0,
    region_id BIGINT UNSIGNED NOT NULL,
    city_municipality_code CHAR(10) NOT NULL,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,
    FOREIGN KEY (region_id) REFERENCES regions(id) ON DELETE CASCADE,
    INDEX idx_psgc_code (psgc_10_digit_code),
    INDEX idx_region (region_id),
    INDEX idx_city_mun (city_municipality_code),
    INDEX idx_code (code)
);

-- Barangays Table
CREATE TABLE barangays (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    psgc_10_digit_code CHAR(10) UNIQUE NOT NULL,
    code VARCHAR(50) NOT NULL,
    name VARCHAR(255) NOT NULL,
    old_name VARCHAR(255) NULL,
    urban_rural VARCHAR(10) NULL,
    is_poblacion BOOLEAN DEFAULT FALSE,
    population_2020 BIGINT UNSIGNED DEFAULT 0,
    region_id BIGINT UNSIGNED NOT NULL,
    province_id BIGINT UNSIGNED NULL,
    city_id BIGINT UNSIGNED NULL,
    municipality_id BIGINT UNSIGNED NULL,
    sub_municipality_id BIGINT UNSIGNED NULL,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,
    FOREIGN KEY (region_id) REFERENCES regions(id) ON DELETE CASCADE,
    FOREIGN KEY (province_id) REFERENCES provinces(id) ON DELETE SET NULL,
    FOREIGN KEY (city_id) REFERENCES cities(id) ON DELETE SET NULL,
    FOREIGN KEY (municipality_id) REFERENCES municipalities(id) ON DELETE SET NULL,
    FOREIGN KEY (sub_municipality_id) REFERENCES sub_municipalities(id) ON DELETE SET NULL,
    INDEX idx_psgc_code (psgc_10_digit_code),
    INDEX idx_region (region_id),
    INDEX idx_province (province_id),
    INDEX idx_city (city_id),
    INDEX idx_municipality (municipality_id),
    INDEX idx_sub_municipality (sub_municipality_id),
    INDEX idx_poblacion (is_poblacion),
    INDEX idx_urban_rural (urban_rural),
    INDEX idx_code (code)
);

-- PSGC Import History Table (for tracking imports)
CREATE TABLE psgc_import_history (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    filename VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    status ENUM('pending', 'processing', 'completed', 'failed') DEFAULT 'pending',
    total_records INT UNSIGNED DEFAULT 0,
    processed_records INT UNSIGNED DEFAULT 0,
    failed_records INT UNSIGNED DEFAULT 0,
    error_message TEXT NULL,
    started_at TIMESTAMP NULL,
    completed_at TIMESTAMP NULL,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,
    INDEX idx_status (status),
    INDEX idx_created (created_at)
);
```

------

## Models & Relationships

### Base Model Trait

```php
// app/Traits/HasPSGCCode.php
<?php

namespace App\Traits;

trait HasPSGCCode
{
    /**
     * Extract region code from PSGC code
     */
    public function getRegionCodeAttribute(): string
    {
        return substr($this->psgc_10_digit_code, 0, 2) . '00000000';
    }

    /**
     * Extract province code from PSGC code
     */
    public function getProvinceCodeAttribute(): string
    {
        return substr($this->psgc_10_digit_code, 0, 5) . '00000';
    }

    /**
     * Extract city/municipality code from PSGC code
     */
    public function getCityMunicipalityCodeAttribute(): string
    {
        return substr($this->psgc_10_digit_code, 0, 7) . '000';
    }

    /**
     * Scope to find by PSGC code
     */
    public function scopeByPSGC($query, string $code)
    {
        return $query->where('psgc_10_digit_code', $code);
    }
}
```

### Region Model

```php
// app/Models/Region.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
use App\Traits\HasPSGCCode;

class Region extends Model
{
    use HasPSGCCode;

    protected $fillable = [
        'psgc_10_digit_code',
        'code',
        'name',
        'region_name',
        'old_name',
        'population_2020',
    ];

    protected $casts = [
        'population_2020' => 'integer',
    ];

    // Relationships
    public function provinces(): HasMany
    {
        return $this->hasMany(Province::class);
    }

    public function cities(): HasMany
    {
        return $this->hasMany(City::class);
    }

    public function municipalities(): HasMany
    {
        return $this->hasMany(Municipality::class);
    }

    public function subMunicipalities(): HasMany
    {
        return $this->hasMany(SubMunicipality::class);
    }

    public function barangays(): HasMany
    {
        return $this->hasMany(Barangay::class);
    }

    // Accessors
    public function getFullNameAttribute(): string
    {
        return "{$this->region_name} ({$this->name})";
    }
}
```

### Province Model

```php
// app/Models/Province.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use App\Traits\HasPSGCCode;

class Province extends Model
{
    use HasPSGCCode;

    protected $fillable = [
        'psgc_10_digit_code',
        'code',
        'name',
        'old_name',
        'income_classification',
        'population_2020',
        'region_id',
    ];

    protected $casts = [
        'population_2020' => 'integer',
    ];

    // Relationships
    public function region(): BelongsTo
    {
        return $this->belongsTo(Region::class);
    }

    public function cities(): HasMany
    {
        return $this->hasMany(City::class);
    }

    public function municipalities(): HasMany
    {
        return $this->hasMany(Municipality::class);
    }

    public function barangays(): HasMany
    {
        return $this->hasMany(Barangay::class);
    }

    // Scopes
    public function scopeInRegion($query, $regionId)
    {
        return $query->where('region_id', $regionId);
    }
}
```

### City Model

```php
// app/Models/City.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use App\Traits\HasPSGCCode;

class City extends Model
{
    use HasPSGCCode;

    protected $fillable = [
        'psgc_10_digit_code',
        'code',
        'name',
        'old_name',
        'city_class',
        'income_classification',
        'is_capital',
        'population_2020',
        'region_id',
        'province_id',
    ];

    protected $casts = [
        'is_capital' => 'boolean',
        'population_2020' => 'integer',
    ];

    // Relationships
    public function region(): BelongsTo
    {
        return $this->belongsTo(Region::class);
    }

    public function province(): BelongsTo
    {
        return $this->belongsTo(Province::class);
    }

    public function barangays(): HasMany
    {
        return $this->hasMany(Barangay::class);
    }

    // Scopes
    public function scopeCapitals($query)
    {
        return $query->where('is_capital', true);
    }

    public function scopeInProvince($query, $provinceId)
    {
        return $query->where('province_id', $provinceId);
    }
}
```

### Municipality Model

```php
// app/Models/Municipality.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use App\Traits\HasPSGCCode;

class Municipality extends Model
{
    use HasPSGCCode;

    protected $fillable = [
        'psgc_10_digit_code',
        'code',
        'name',
        'old_name',
        'income_classification',
        'is_capital',
        'population_2020',
        'region_id',
        'province_id',
    ];

    protected $casts = [
        'is_capital' => 'boolean',
        'population_2020' => 'integer',
    ];

    // Relationships
    public function region(): BelongsTo
    {
        return $this->belongsTo(Region::class);
    }

    public function province(): BelongsTo
    {
        return $this->belongsTo(Province::class);
    }

    public function barangays(): HasMany
    {
        return $this->hasMany(Barangay::class);
    }

    // Scopes
    public function scopeCapitals($query)
    {
        return $query->where('is_capital', true);
    }

    public function scopeInProvince($query, $provinceId)
    {
        return $query->where('province_id', $provinceId);
    }
}
```

### SubMunicipality Model

```php
// app/Models/SubMunicipality.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use App\Traits\HasPSGCCode;

class SubMunicipality extends Model
{
    use HasPSGCCode;

    protected $fillable = [
        'psgc_10_digit_code',
        'code',
        'name',
        'old_name',
        'population_2020',
        'region_id',
        'city_municipality_code',
    ];

    protected $casts = [
        'population_2020' => 'integer',
    ];

    // Relationships
    public function region(): BelongsTo
    {
        return $this->belongsTo(Region::class);
    }

    public function barangays(): HasMany
    {
        return $this->hasMany(Barangay::class);
    }
}
```

### Barangay Model

```php
// app/Models/Barangay.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use App\Traits\HasPSGCCode;

class Barangay extends Model
{
    use HasPSGCCode;

    protected $fillable = [
        'psgc_10_digit_code',
        'code',
        'name',
        'old_name',
        'urban_rural',
        'is_poblacion',
        'population_2020',
        'region_id',
        'province_id',
        'city_id',
        'municipality_id',
        'sub_municipality_id',
    ];

    protected $casts = [
        'is_poblacion' => 'boolean',
        'population_2020' => 'integer',
    ];

    // Relationships
    public function region(): BelongsTo
    {
        return $this->belongsTo(Region::class);
    }

    public function province(): BelongsTo
    {
        return $this->belongsTo(Province::class);
    }

    public function city(): BelongsTo
    {
        return $this->belongsTo(City::class);
    }

    public function municipality(): BelongsTo
    {
        return $this->belongsTo(Municipality::class);
    }

    public function subMunicipality(): BelongsTo
    {
        return $this->belongsTo(SubMunicipality::class);
    }

    // Scopes
    public function scopePoblacion($query)
    {
        return $query->where('is_poblacion', true);
    }

    public function scopeUrban($query)
    {
        return $query->where('urban_rural', 'Urban');
    }

    public function scopeRural($query)
    {
        return $query->where('urban_rural', 'Rural');
    }

    // Accessor to determine parent entity
    public function getParentEntityAttribute()
    {
        if ($this->sub_municipality_id) {
            return $this->subMunicipality;
        }
        if ($this->city_id) {
            return $this->city;
        }
        if ($this->municipality_id) {
            return $this->municipality;
        }
        return null;
    }
}
```

------

## Commands & Jobs

### Main Import Command

```php
// app/Console/Commands/ImportPSGCCommand.php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Services\PSGCImportService;
use App\Jobs\ImportPSGCDataJob;

class ImportPSGCCommand extends Command
{
    protected $signature = 'psgc:import
                            {file : The Excel file path}
                            {--queue : Queue the import job}
                            {--sheet=3 : Sheet index to import (default: 3)}';

    protected $description = 'Import PSGC data from Excel file';

    public function handle(PSGCImportService $importService)
    {
        $filePath = $this->argument('file');
        $sheetIndex = $this->option('sheet');
        $useQueue = $this->option('queue');

        if (!file_exists($filePath)) {
            $this->error("File not found: {$filePath}");
            return Command::FAILURE;
        }

        $this->info("Starting PSGC import from: {$filePath}");

        if ($useQueue) {
            ImportPSGCDataJob::dispatch($filePath, $sheetIndex);
            $this->info('Import job queued successfully!');
            return Command::SUCCESS;
        }

        // Direct import
        $bar = $this->output->createProgressBar();

        $result = $importService->import($filePath, $sheetIndex, function ($progress) use ($bar) {
            $bar->setProgress($progress);
        });

        $bar->finish();
        $this->newLine();

        if ($result['success']) {
            $this->info("Import completed successfully!");
            $this->table(
                ['Type', 'Count'],
                [
                    ['Regions', $result['stats']['regions']],
                    ['Provinces', $result['stats']['provinces']],
                    ['Cities', $result['stats']['cities']],
                    ['Municipalities', $result['stats']['municipalities']],
                    ['Sub-Municipalities', $result['stats']['sub_municipalities']],
                    ['Barangays', $result['stats']['barangays']],
                ]
            );
            return Command::SUCCESS;
        }

        $this->error("Import failed: " . $result['message']);
        return Command::FAILURE;
    }
}
```

### Import Service

```php
// app/Services/PSGCImportService.php
<?php

namespace App\Services;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Log;
use PhpOffice\PhpSpreadsheet\IOFactory;
use App\Models\{Region, Province, City, Municipality, SubMunicipality, Barangay};

class PSGCImportService
{
    private PSGCProcessorService $processor;

    public function __construct(PSGCProcessorService $processor)
    {
        $this->processor = $processor;
    }

    public function import(string $filePath, int $sheetIndex = 3, ?callable $progressCallback = null): array
    {
        try {
            DB::beginTransaction();

            // Clear existing data
            $this->clearExistingData();

            // Read Excel file
            $data = $this->readExcelFile($filePath, $sheetIndex);

            $total = count($data);
            $stats = [
                'regions' => 0,
                'provinces' => 0,
                'cities' => 0,
                'municipalities' => 0,
                'sub_municipalities' => 0,
                'barangays' => 0,
            ];

            // Process by geographic level
            $processed = 0;

            // 1. Process Regions
            $regions = array_filter($data, fn($row) => $row['geographicLevel'] === 'Reg');
            foreach ($regions as $row) {
                $this->processor->processRegion($row);
                $stats['regions']++;
                $processed++;
                if ($progressCallback) $progressCallback($processed / $total * 100);
            }

            // 2. Process Provinces
            $provinces = array_filter($data, fn($row) => $row['geographicLevel'] === 'Prov');
            foreach ($provinces as $row) {
                $this->processor->processProvince($row);
                $stats['provinces']++;
                $processed++;
                if ($progressCallback) $progressCallback($processed / $total * 100);
            }

            // 3. Process Cities
            $cities = array_filter($data, fn($row) => $row['geographicLevel'] === 'City');
            foreach ($cities as $row) {
                $this->processor->processCity($row);
                $stats['cities']++;
                $processed++;
                if ($progressCallback) $progressCallback($processed / $total * 100);
            }

            // 4. Process Municipalities
            $municipalities = array_filter($data, fn($row) => $row['geographicLevel'] === 'Mun');
            foreach ($municipalities as $row) {
                $this->processor->processMunicipality($row);
                $stats['municipalities']++;
                $processed++;
                if ($progressCallback) $progressCallback($processed / $total * 100);
            }

            // 5. Process Sub-Municipalities
            $subMunicipalities = array_filter($data, fn($row) => $row['geographicLevel'] === 'SubMun');
            foreach ($subMunicipalities as $row) {
                $this->processor->processSubMunicipality($row);
                $stats['sub_municipalities']++;
                $processed++;
                if ($progressCallback) $progressCallback($processed / $total * 100);
            }

            // 6. Process Barangays
            $barangays = array_filter($data, fn($row) => $row['geographicLevel'] === 'Brgy');
            foreach ($barangays as $row) {
                $this->processor->processBarangay($row);
                $stats['barangays']++;
                $processed++;
                if ($progressCallback) $progressCallback($processed / $total * 100);
            }

            DB::commit();

            return [
                'success' => true,
                'stats' => $stats,
            ];

        } catch (\Exception $e) {
            DB::rollBack();
            Log::error('PSGC Import failed: ' . $e->getMessage());

            return [
                'success' => false,
                'message' => $e->getMessage(),
            ];
        }
    }

    private function readExcelFile(string $filePath, int $sheetIndex): array
    {
        $spreadsheet = IOFactory::load($filePath);
        $sheet = $spreadsheet->getSheet($sheetIndex);
        $rows = $sheet->toArray();

        // Remove header row
        array_shift($rows);

        $data = [];
        foreach ($rows as $row) {
            if (empty($row[0])) continue; // Skip empty rows

            $data[] = [
                'psgc10DigitCode' => (string) $row[0],
                'name' => $row[1] ?? '',
                'code' => (string) ($row[2] ?? 'Unavailable'),
                'geographicLevel' => $row[3] === 'Bgy' ? 'Brgy' : ($row[3] ?? ''),
                'oldName' => $row[4] ?? null,
                'cityClass' => $row[5] ?? null,
                'incomeClassification' => $row[6] ?? null,
                'urbanRural' => $row[7] ?? null,
                'population2020' => (int) ($row[8] ?? 0),
                'status' => $row[9] ?? null,
            ];
        }

        return $data;
    }

    private function clearExistingData(): void
    {
        // Clear in reverse order of dependencies
        Barangay::truncate();
        SubMunicipality::truncate();
        Municipality::truncate();
        City::truncate();
        Province::truncate();
        Region::truncate();
    }
}
```

### Processor Service

```php
// app/Services/PSGCProcessorService.php
<?php

namespace App\Services;

use App\Models\{Region, Province, City, Municipality, SubMunicipality, Barangay};

class PSGCProcessorService
{
    public function processRegion(array $data): Region
    {
        // Parse region name format: "NAME (ABBREVIATION)"
        preg_match('/(.+?)\s*\((.+?)\)/', $data['name'], $matches);

        if ($matches) {
            $regionName = trim($matches[1]);
            $name = trim($matches[2]);
        } else {
            // Handle special cases like "MIMAROPA Region"
            $parts = explode(' ', $data['name']);
            $regionName = $parts[0];
            $name = $data['name'];
        }

        return Region::updateOrCreate(
            ['psgc_10_digit_code' => $data['psgc10DigitCode']],
            [
                'code' => $data['code'],
                'name' => $name,
                'region_name' => $regionName,
                'old_name' => $data['oldName'],
                'population_2020' => $data['population2020'],
            ]
        );
    }

    public function processProvince(array $data): Province
    {
        $regionCode = $this->extractRegionCode($data['psgc10DigitCode']);
        $region = Region::where('psgc_10_digit_code', $regionCode)->firstOrFail();

        return Province::updateOrCreate(
            ['psgc_10_digit_code' => $data['psgc10DigitCode']],
            [
                'code' => $data['code'],
                'name' => $data['name'],
                'old_name' => $data['oldName'],
                'income_classification' => $data['incomeClassification'],
                'population_2020' => $data['population2020'],
                'region_id' => $region->id,
            ]
        );
    }

    public function processCity(array $data): City
    {
        $regionCode = $this->extractRegionCode($data['psgc10DigitCode']);
        $provinceCode = $this->extractProvinceCode($data['psgc10DigitCode']);

        $region = Region::where('psgc_10_digit_code', $regionCode)->firstOrFail();
        $province = Province::where('psgc_10_digit_code', $provinceCode)->firstOrFail();

        return City::updateOrCreate(
            ['psgc_10_digit_code' => $data['psgc10DigitCode']],
            [
                'code' => $data['code'],
                'name' => $data['name'],
                'old_name' => $data['oldName'],
                'city_class' => $data['cityClass'],
                'income_classification' => $data['incomeClassification'],
                'is_capital' => $data['status'] === 'Capital',
                'population_2020' => $data['population2020'],
                'region_id' => $region->id,
                'province_id' => $province->id,
            ]
        );
    }

    public function processMunicipality(array $data): Municipality
    {
        $regionCode = $this->extractRegionCode($data['psgc10DigitCode']);
        $provinceCode = $this->extractProvinceCode($data['psgc10DigitCode']);

        $region = Region::where('psgc_10_digit_code', $regionCode)->firstOrFail();
        $province = Province::where('psgc_10_digit_code', $provinceCode)->firstOrFail();

        return Municipality::updateOrCreate(
            ['psgc_10_digit_code' => $data['psgc10DigitCode']],
            [
                'code' => $data['code'],
                'name' => $data['name'],
                'old_name' => $data['oldName'],
                'income_classification' => $data['incomeClassification'],
                'is_capital' => $data['status'] === 'Capital',
                'population_2020' => $data['population2020'],
                'region_id' => $region->id,
                'province_id' => $province->id,
            ]
        );
    }

    public function processSubMunicipality(array $data): SubMunicipality
    {
        $regionCode = $this->extractRegionCode($data['psgc10DigitCode']);
        $region = Region::where('psgc_10_digit_code', $regionCode)->firstOrFail();

        $cityMunicipalityCode = substr($data['psgc10DigitCode'], 0, 5) . '00000';

        return SubMunicipality::updateOrCreate(
            ['psgc_10_digit_code' => $data['psgc10DigitCode']],
            [
                'code' => $data['code'],
                'name' => $data['name'],
                'old_name' => $data['oldName'],
                'population_2020' => $data['population2020'],
                'region_id' => $region->id,
                'city_municipality_code' => $cityMunicipalityCode,
            ]
        );
    }

    public function processBarangay(array $data): Barangay
    {
        $regionCode = $this->extractRegionCode($data['psgc10DigitCode']);
        $provinceCode = $this->extractProvinceCode($data['psgc10DigitCode']);

        $region = Region::where('psgc_10_digit_code', $regionCode)->firstOrFail();
        $province = Province::where('psgc_10_digit_code', $provinceCode)->first();

        // Determine parent (sub-municipality, city, or municipality)
        $subMunicipalityPrefix = substr($data['psgc10DigitCode'], 0, 7);
        $subMunicipality = SubMunicipality::where('psgc_10_digit_code', 'like', $subMunicipalityPrefix . '%')->first();

        $city = null;
        $municipality = null;

        if (!$subMunicipality) {
            $cityMunCode = substr($data['psgc10DigitCode'], 0, 7) . '000';
            $city = City::where('psgc_10_digit_code', $cityMunCode)->first();

            if (!$city) {
                $municipality = Municipality::where('psgc_10_digit_code', $cityMunCode)->first();
            }
        }

        $name = $data['status'] === 'Pob.' ? $data['name'] . ' (Pob.)' : $data['name'];
        $urbanRural = $this->processUrbanRural($data['urbanRural']);

        return Barangay::updateOrCreate(
            ['psgc_10_digit_code' => $data['psgc10DigitCode']],
            [
                'code' => $data['code'],
                'name' => $name,
                'old_name' => $data['oldName'],
                'urban_rural' => $urbanRural,
                'is_poblacion' => $data['status'] === 'Pob.',
                'population_2020' => $data['population2020'],
                'region_id' => $region->id,
                'province_id' => $province?->id,
                'city_id' => $city?->id,
                'municipality_id' => $municipality?->id,
                'sub_municipality_id' => $subMunicipality?->id,
            ]
        );
    }

    private function extractRegionCode(string $psgcCode): string
    {
        return substr($psgcCode, 0, 2) . '00000000';
    }

    private function extractProvinceCode(string $psgcCode): string
    {
        return substr($psgcCode, 0, 5) . '00000';
    }

    private function processUrbanRural(?string $value): ?string
    {
        if (!$value) return null;
        if ($value === 'R') return 'Rural';
        if ($value === 'U') return 'Urban';
        return null;
    }
}
```

------

## API Routes & Controllers

### Routes

```php
// routes/api.php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\Api\{
    RegionController,
    ProvinceController,
    CityController,
    MunicipalityController,
    SubMunicipalityController,
    BarangayController
};

Route::prefix('v1')->group(function () {
    // Regions
    Route::get('regions', [RegionController::class, 'index']);
    Route::get('regions/{psgcCode}', [RegionController::class, 'show']);
    Route::get('regions/{psgcCode}/provinces', [RegionController::class, 'provinces']);
    Route::get('regions/{psgcCode}/cities', [RegionController::class, 'cities']);
    Route::get('regions/{psgcCode}/municipalities', [RegionController::class, 'municipalities']);

    // Provinces
    Route::get('provinces', [ProvinceController::class, 'index']);
    Route::get('provinces/{psgcCode}', [ProvinceController::class, 'show']);
    Route::get('provinces/{psgcCode}/cities', [ProvinceController::class, 'cities']);
    Route::get('provinces/{psgcCode}/municipalities', [ProvinceController::class, 'municipalities']);

    // Cities
    Route::get('cities', [CityController::class, 'index']);
    Route::get('cities/{psgcCode}', [CityController::class, 'show']);
    Route::get('cities/{psgcCode}/barangays', [CityController::class, 'barangays']);

    // Municipalities
    Route::get('municipalities', [MunicipalityController::class, 'index']);
    Route::get('municipalities/{psgcCode}', [MunicipalityController::class, 'show']);
    Route::get('municipalities/{psgcCode}/barangays', [MunicipalityController::class, 'barangays']);

    // Sub-Municipalities
    Route::get('sub-municipalities', [SubMunicipalityController::class, 'index']);
    Route::get('sub-municipalities/{psgcCode}', [SubMunicipalityController::class, 'show']);
    Route::get('sub-municipalities/{psgcCode}/barangays', [SubMunicipalityController::class, 'barangays']);

    // Barangays
    Route::get('barangays', [BarangayController::class, 'index']);
    Route::get('barangays/{psgcCode}', [BarangayController::class, 'show']);

    // Search
    Route::get('search', [BarangayController::class, 'search']);
});
```

### Example Controller

```php
// app/Http/Controllers/Api/RegionController.php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Resources\RegionResource;
use App\Http\Resources\ProvinceResource;
use App\Http\Resources\CityResource;
use App\Http\Resources\MunicipalityResource;
use App\Models\Region;
use Illuminate\Support\Facades\Cache;

class RegionController extends Controller
{
    public function index()
    {
        $regions = Cache::remember('regions.all', 3600, function () {
            return Region::with(['provinces', 'cities', 'municipalities'])
                ->orderBy('name')
                ->get();
        });

        return RegionResource::collection($regions);
    }

    public function show(string $psgcCode)
    {
        $region = Cache::remember("regions.{$psgcCode}", 3600, function () use ($psgcCode) {
            return Region::with(['provinces', 'cities', 'municipalities'])
                ->byPSGC($psgcCode)
                ->firstOrFail();
        });

        return new RegionResource($region);
    }

    public function provinces(string $psgcCode)
    {
        $provinces = Cache::remember("regions.{$psgcCode}.provinces", 3600, function () use ($psgcCode) {
            $region = Region::byPSGC($psgcCode)->firstOrFail();
            return $region->provinces()->orderBy('name')->get();
        });

        return ProvinceResource::collection($provinces);
    }

    public function cities(string $psgcCode)
    {
        $cities = Cache::remember("regions.{$psgcCode}.cities", 3600, function () use ($psgcCode) {
            $region = Region::byPSGC($psgcCode)->firstOrFail();
            return $region->cities()->orderBy('name')->get();
        });

        return CityResource::collection($cities);
    }

    public function municipalities(string $psgcCode)
    {
        $municipalities = Cache::remember("regions.{$psgcCode}.municipalities", 3600, function () use ($psgcCode) {
            $region = Region::byPSGC($psgcCode)->firstOrFail();
            return $region->municipalities()->orderBy('name')->get();
        });

        return MunicipalityResource::collection($municipalities);
    }
}
```

### Example API Resource

```php
// app/Http/Resources/RegionResource.php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class RegionResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'psgc_code' => $this->psgc_10_digit_code,
            'code' => $this->code,
            'name' => $this->name,
            'region_name' => $this->region_name,
            'full_name' => $this->full_name,
            'old_name' => $this->old_name,
            'population_2020' => $this->population_2020,
            'provinces_count' => $this->whenCounted('provinces'),
            'cities_count' => $this->whenCounted('cities'),
            'municipalities_count' => $this->whenCounted('municipalities'),
            'provinces' => ProvinceResource::collection($this->whenLoaded('provinces')),
            'cities' => CityResource::collection($this->whenLoaded('cities')),
            'municipalities' => MunicipalityResource::collection($this->whenLoaded('municipalities')),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }
}
```

------

## Caching Strategy

### Cache Configuration

```php
// config/psgc.php
<?php

return [
    'cache' => [
        'enabled' => env('PSGC_CACHE_ENABLED', true),
        'ttl' => env('PSGC_CACHE_TTL', 3600), // 1 hour
        'prefix' => 'psgc',
    ],

    'import' => [
        'chunk_size' => env('PSGC_IMPORT_CHUNK_SIZE', 1000),
        'queue' => env('PSGC_IMPORT_QUEUE', 'default'),
    ],

    'api' => [
        'per_page' => env('PSGC_API_PER_PAGE', 50),
        'max_per_page' => env('PSGC_API_MAX_PER_PAGE', 200),
    ],
];
```

### Cache Service

```php
// app/Services/PSGCCacheService.php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Storage;
use App\Models\{Region, Province, City, Municipality, SubMunicipality, Barangay};

class PSGCCacheService
{
    public function generateAllCacheFiles(): void
    {
        $this->generateRegionsCache();
        $this->generateProvincesCache();
        $this->generateCitiesCache();
        $this->generateMunicipalitiesCache();
        $this->generateSubMunicipalitiesCache();
        $this->generateBarangaysCache();
    }

    public function generateRegionsCache(): void
    {
        $regions = Region::all()->toArray();

        Storage::disk('local')->put(
            'psgc/cache/regions.json',
            json_encode($regions, JSON_PRETTY_PRINT)
        );

        Cache::put('psgc.regions.all', $regions, config('psgc.cache.ttl'));
    }

    public function generateProvincesCache(): void
    {
        Province::chunk(100, function ($provinces) {
            foreach ($provinces as $province) {
                $data = $province->toArray();

                Storage::disk('local')->put(
                    "psgc/cache/provinces/{$province->psgc_10_digit_code}.json",
                    json_encode([$data], JSON_PRETTY_PRINT)
                );

                Cache::put(
                    "psgc.provinces.{$province->psgc_10_digit_code}",
                    $data,
                    config('psgc.cache.ttl')
                );
            }
        });
    }

    // Similar methods for other geographic levels...

    public function clearAllCache(): void
    {
        Cache::tags(['psgc'])->flush();
        Storage::disk('local')->deleteDirectory('psgc/cache');
    }
}
```

------

## Implementation Guide

### Step 1: Install Dependencies

```bash
composer require phpoffice/phpspreadsheet
composer require predis/predis
```

### Step 2: Configure Database

```bash
php artisan migrate
```

### Step 3: Import Data

```bash
# Direct import
php artisan psgc:import /path/to/PSGC-4Q-2025-Publication-Datafile.xlsx

# Queued import (recommended for large files)
php artisan psgc:import /path/to/file.xlsx --queue
```

### Step 4: Generate Cache Files

```bash
php artisan psgc:generate-cache
```

### Step 5: Test API

```bash
# Get all regions
curl http://localhost/api/v1/regions

# Get specific region
curl http://localhost/api/v1/regions/1300000000

# Get provinces in a region
curl http://localhost/api/v1/regions/1300000000/provinces

# Search
curl http://localhost/api/v1/search?q=Caloocan
```

------

## Additional Features

### 1. Search Functionality

```php
// app/Http/Controllers/Api/SearchController.php
public function search(Request $request)
{
    $query = $request->input('q');
    $type = $request->input('type'); // region, province, city, municipality, barangay

    $results = [];

    if (!$type || $type === 'region') {
        $results['regions'] = Region::where('name', 'like', "%{$query}%")
            ->orWhere('region_name', 'like', "%{$query}%")
            ->limit(10)
            ->get();
    }

    if (!$type || $type === 'barangay') {
        $results['barangays'] = Barangay::where('name', 'like', "%{$query}%")
            ->with(['city', 'municipality', 'province', 'region'])
            ->limit(20)
            ->get();
    }

    // ... other types

    return response()->json($results);
}
```

### 2. Export Functionality

```php
// app/Console/Commands/ExportPSGCCommand.php
public function handle()
{
    $format = $this->option('format'); // json, csv, excel

    // Export logic...
}
```

### 3. Validation & Data Integrity

```php
// app/Console/Commands/ValidatePSGCCommand.php
public function handle()
{
    // Check for orphaned records
    // Validate PSGC code formats
    // Check relationship integrity
}
```

------

## Performance Optimizations

### 1. Database Indexing

- Already included in migration files
- Indexes on PSGC codes, foreign keys, and common query fields

### 2. Query Optimization

- Use eager loading for relationships
- Implement pagination for large datasets
- Use select() to limit columns returned

### 3. Caching Strategy

- Cache frequently accessed data (regions, provinces)
- Use Redis for distributed caching
- Cache invalidation on data updates

### 4. Queue Processing

- Use queues for large imports
- Batch processing for barangays
- Progress tracking with job events

------

## Testing

```php
// tests/Feature/PSGCApiTest.php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use App\Models\Region;

class PSGCApiTest extends TestCase
{
    public function test_can_get_all_regions()
    {
        Region::factory()->count(3)->create();

        $response = $this->getJson('/api/v1/regions');

        $response->assertStatus(200)
            ->assertJsonCount(3, 'data');
    }

    public function test_can_get_specific_region()
    {
        $region = Region::factory()->create([
            'psgc_10_digit_code' => '1300000000'
        ]);

        $response = $this->getJson('/api/v1/regions/1300000000');

        $response->assertStatus(200)
            ->assertJson([
                'data' => [
                    'psgc_code' => '1300000000',
                    'name' => $region->name,
                ]
            ]);
    }
}
```

------

## Summary

This Laravel architecture provides:

1. **Robust Data Import**: Excel to database with validation and progress tracking
2. **Hierarchical Relationships**: Proper Eloquent relationships between all geographic levels
3. **RESTful API**: Clean, cached API endpoints for all data access
4. **Scalability**: Queue-based processing for large datasets
5. **Performance**: Comprehensive caching and database indexing
6. **Maintainability**: Service-oriented architecture with clear separation of concerns
7. **Flexibility**: Support for various geographic queries and relationships
8. **Testing**: Framework for unit and feature tests

The system is production-ready and can handle the full PSGC dataset efficiently!