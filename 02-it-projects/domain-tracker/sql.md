# SQL

## MySQL

```sql
CREATE TABLE `domains` (
  `id` int(11) NOT NULL,
  `domain_name` varchar(255) NOT NULL,
  `dns_manager` enum('DigitalOcean','Cloudflare','Namecheap') NOT NULL,
  `has_mx_records` tinyint(1) NOT NULL DEFAULT 0,
  `mx_provider` enum('Google','Microsoft','Zoho','None') NOT NULL DEFAULT 'None',
  `status` enum('Active','Pending','Expired','Transferring','Inactive') NOT NULL DEFAULT 'Active',
  `remarks` varchar(255) NOT NULL,
  `tasks` varchar(255) NOT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp()
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

ALTER TABLE `domains`
  ADD PRIMARY KEY (`id`),
  ADD UNIQUE KEY `domain_name` (`domain_name`);

ALTER TABLE `domains`
  MODIFY `id` int(11) NOT NULL AUTO_INCREMENT;

CREATE TABLE `subdomains` (
  `id` int(11) NOT NULL,
  `domain_id` int(11) NOT NULL,
  `subdomain_name` varchar(255) NOT NULL,
  `dns_manager` enum('DigitalOcean','Cloudflare','Namecheap','GreenGeeks','Others') NOT NULL DEFAULT 'Namecheap',
  `status` enum('Active','Pending','Inactive','Maintenance','Decommissioned') NOT NULL DEFAULT 'Active',
  `project` enum('MedProjects','HealthSpace','Kranium','QC HIS','Others') NOT NULL,
  `cloud_provider` enum('DigitalOcean','Azure','GreenGeeks','Others') NOT NULL DEFAULT 'DigitalOcean',
  `ip_address` varchar(45) DEFAULT NULL,
  `remarks` text DEFAULT NULL,
  `tasks` text DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp()
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

ALTER TABLE `subdomains`
  ADD PRIMARY KEY (`id`),
  ADD UNIQUE KEY `subdomain_domain` (`subdomain_name`,`domain_id`),
  ADD KEY `domain_id` (`domain_id`);

ALTER TABLE `subdomains`
  MODIFY `id` int(11) NOT NULL AUTO_INCREMENT;

ALTER TABLE `subdomains`
  ADD CONSTRAINT `subdomain_domain_fk` FOREIGN KEY (`domain_id`) REFERENCES `domains` (`id`) ON DELETE CASCADE ON UPDATE CASCADE;

```

## SQLite

```sqlite
-- SQLite Domain Management Schema

-- Domains table
CREATE TABLE domains (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    domain_name TEXT NOT NULL UNIQUE,
    dns_manager TEXT NOT NULL CHECK (dns_manager IN ('DigitalOcean', 'Cloudflare', 'Namecheap')),
    has_mx_records INTEGER NOT NULL DEFAULT 0 CHECK (has_mx_records IN (0, 1)),
    mx_provider TEXT NOT NULL DEFAULT 'None' CHECK (mx_provider IN ('Google', 'Microsoft', 'Zoho', 'None')),
    status TEXT NOT NULL DEFAULT 'Active' CHECK (status IN ('Active', 'Pending', 'Expired', 'Transferring', 'Inactive')),
    remarks TEXT NOT NULL DEFAULT '',
    tasks TEXT NOT NULL DEFAULT '',
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Trigger to update updated_at timestamp for domains
CREATE TRIGGER update_domains_timestamp
    AFTER UPDATE ON domains
    FOR EACH ROW
BEGIN
    UPDATE domains SET updated_at = CURRENT_TIMESTAMP WHERE id = NEW.id;
END;

-- Subdomains table
CREATE TABLE subdomains (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    domain_id INTEGER NOT NULL,
    subdomain_name TEXT NOT NULL,
    dns_manager TEXT NOT NULL DEFAULT 'Namecheap' CHECK (dns_manager IN ('DigitalOcean', 'Cloudflare', 'Namecheap', 'GreenGeeks', 'Others')),
    status TEXT NOT NULL DEFAULT 'Active' CHECK (status IN ('Active', 'Pending', 'Inactive', 'Maintenance', 'Decommissioned')),
    project TEXT NOT NULL CHECK (project IN ('MedProjects', 'HealthSpace', 'Kranium', 'QC HIS', 'Others')),
    cloud_provider TEXT NOT NULL DEFAULT 'DigitalOcean' CHECK (cloud_provider IN ('DigitalOcean', 'Azure', 'GreenGeeks', 'Others')),
    ip_address TEXT,
    remarks TEXT,
    tasks TEXT,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (domain_id) REFERENCES domains(id) ON DELETE CASCADE ON UPDATE CASCADE,
    UNIQUE(subdomain_name, domain_id)
);

-- Trigger to update updated_at timestamp for subdomains
CREATE TRIGGER update_subdomains_timestamp
    AFTER UPDATE ON subdomains
    FOR EACH ROW
BEGIN
    UPDATE subdomains SET updated_at = CURRENT_TIMESTAMP WHERE id = NEW.id;
END;

-- Indexes for better performance
CREATE INDEX idx_domains_status ON domains(status);
CREATE INDEX idx_domains_dns_manager ON domains(dns_manager);
CREATE INDEX idx_subdomains_domain_id ON subdomains(domain_id);
CREATE INDEX idx_subdomains_status ON subdomains(status);
CREATE INDEX idx_subdomains_project ON subdomains(project);
CREATE INDEX idx_subdomains_cloud_provider ON subdomains(cloud_provider);
```

