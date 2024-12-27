Create Database ShiftHandoverDB

Use ShiftHandoverDB

--Employee Table
Create Table Employees(
EmployeeID int identity(1,1) Primary key,
Name Nvarchar(100)NOT null,
Email Nvarchar(100)NOT NULL,
PhoneNumber nvarchar(15),
Role Nvarchar (50),-- e.g., Operator, Team Leader, Shift Manager
ShiftAssgnedID int NULL
);
select * from Employees


--shifts details Table
create Table Shifts(
ShiftID int identity(1,1)Primary key,
StartTime DateTime NOT null,
EndTime DateTime NOT NULL,
ShiftType Nvarchar(20) not NULL, -- e.g., Day, Night
OutgoingOperatorID int NUll references Employees(EmployeeID),
IncomingOperatorID int NULL references Employees(EmployeeID)  
);
Drop table Shifts
select * from shifts

-- Machines Table
Create Table MachineDetails (
MachineID INT identity (1,1) Primary key,
MachineName nvarchar(100)NOT NULL,
LineID INT NOT NULL,
Status Nvarchar(20)NOT NULL,-- e.g., Operational, Under Maintenance
StatusCode int NOT NULL,
FOREIGN KEY (LineID) REFERENCES LinesConfig(LineID)
);

select * from MachineDetails
--LINES Table
Create Table LinesConfig(
LineID int Identity(1,1)primary key,
LineName nvarchar(50)NOT NULL,
Description nvarchar(255),
OperatorID int  REFERENCES Employees(EmployeeID),
TeamLeaderID int NUll References Employees(EmployeeID)
);

select* from LinesConfig

--issues table

Create Table IssuesReports(
IssueID int Identity (1,1) primary key,
IssueDescription Nvarchar(Max),
ReportedBy INT not NULL References Employees(EmployeeID),
ReportedATt DateTime NOT NULL Default GETDate(),
Status Nvarchar(20)Default 'open',--open , resolved and inprocess
ResolutionDescription nvarchar(max) ,
ResolvedBy int NULL References Employees(EmployeeID),
ResolvedAt DateTime,
);

select* from IssuesReports

-- Handover Logs Table
Create table HandoverLogs(
HandoverID int identity(1,1) Primary key,
ShiftID INT not NULL references Shifts(ShiftID),
outgoingOperatorNotes nvarchar (max),
IncomingOpreatorNotes nvarchar(max),
TeamLeaderNotes nvarchar(max),
ShiftManagerNotes nvarchar(max),
PerformanceFigures NVARCHAR(MAX), -- JSON format for flexibility
CreatedAt DATETIME NOT NULL DEFAULT GETDATE()
);

select * from HandoverLogs

-- Production Table

create table ProductionDetails(
ProductionID int identity(1,1) primary key,
LineID int not null references LinesConfig(LineID),
ShiftID int not null references Shifts(shiftID),
ProductName nvarchar(100),
BatchNumber nvarchar (50),
StartTime dateTime,
EndTime dateTime,
OutputQuantity int ,
RejectedQuantity INT,
PerformanceMetrics nvarchar(max),
Remarks nvarchar (max)-- JSON format for detailed metrics
);

select * from ProductionDetails

-- Performance Metrics Table (Optional for flexibility)
CREATE TABLE PerformanceMetrics (
    MetricID INT IDENTITY(1,1) PRIMARY KEY,
    ShiftID INT NOT NULL REFERENCES Shifts(ShiftID),
    MetricName NVARCHAR(100), -- e.g., Downtime, Output
    MetricValue FLOAT,
    RecordedAt DATETIME NOT NULL DEFAULT GETDATE()
);

create table Notifications(
NotificationID int identity(1,1) primary key,
Recipitent int NOT null references Employees(EmployeeID),
Message nvarchar(Max),
SentAt DateTime NOT null Default getdate(),
ReadStatus BIT DEFAULT 0-- 0: Unread, 1: Read);
);

Select * from Notifications

Create table Plans (
PlansID INT identity (1,1) primary key,
shiftID int not null references Shifts(ShiftID),
PlannedTask nvarchar(max),
Changes Nvarchar (max),
ReasonForChange nvarchar(max),
UpdatedBy int not null references Employees(EmployeeID),
UpdatedAt datetime NOT null Default GetDATE()

);

Select * from Plans

--create table systemAuditLogs

create table SystemAuditLogs(
    LogID INT IDENTITY(1,1) PRIMARY KEY,
    ActionType NVARCHAR(100), -- e.g., Update Issue, Add Handover Notes
    PerformedBy INT NOT NULL REFERENCES Employees(EmployeeID),
    PerformedAt DATETIME NOT NULL DEFAULT GETDATE(),
    Details NVARCHAR(MAX)
);

select * from SystemAuditLogs
