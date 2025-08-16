import pandas as pd
import numpy as np
from faker import Faker
import random
from datetime import timedelta

fake = Faker()

# -----------------------------
# Configuration
# -----------------------------
num_records_fact = 500_000
num_records_dim = 500

# -----------------------------
# Generate Dimension Tables (4 attributes each)
# -----------------------------
def generate_dim_patient(n):
    return pd.DataFrame({
        'P_PatientID': range(1, n+1),
        'PatientName': [fake.name() for _ in range(n)],
        'Gender': [random.choice(['Male','Female']) for _ in range(n)],
        'DOB': [fake.date_of_birth(minimum_age=0, maximum_age=90) for _ in range(n)],
        'City': [fake.city() for _ in range(n)],
        'State': [fake.state() for _ in range(n)]
    })

def generate_dim_doctor(n):
    return pd.DataFrame({
        'D_DoctorID': range(1, n+1),
        'DoctorName': [fake.name() for _ in range(n)],
        'Specialty': [random.choice(['Cardiology','Orthopedics','Neurology','General']) for _ in range(n)],
        'ExperienceYears': [random.randint(1,40) for _ in range(n)],
        'Department': [random.choice(['Surgery','Cardiology','Neurology','Orthopedics']) for _ in range(n)],
        'ContactNumber': [fake.phone_number() for _ in range(n)]
    })

def generate_dim_hospital(n):
    return pd.DataFrame({
        'H_HospitalID': range(1, n+1),
        'HospitalName': [fake.company() for _ in range(n)],
        'City': [fake.city() for _ in range(n)],
        'State': [fake.state() for _ in range(n)],
        'Capacity': [random.randint(50,500) for _ in range(n)],
        'AccreditationLevel': [random.choice(['A','B','C']) for _ in range(n)]
    })

def generate_dim_department(n):
    return pd.DataFrame({
        'Dept_DepartmentID': range(1, n+1),
        'DepartmentName': [random.choice(['Surgery','Cardiology','Neurology','Orthopedics']) for _ in range(n)],
        'Floor': [random.randint(1,10) for _ in range(n)],
        'HeadDoctorID': [random.randint(1,n) for _ in range(n)],
        'RoomCount': [random.randint(5,50) for _ in range(n)],
        'Budget': [random.randint(10000,500000) for _ in range(n)]
    })

def generate_dim_lab_test(n):
    return pd.DataFrame({
        'L_LabTestID': range(1, n+1),
        'TestName': [fake.word().title() for _ in range(n)],
        'Category': [random.choice(['Blood','Urine','Imaging','Pathology']) for _ in range(n)],
        'NormalRange': [f"{random.randint(10,50)}-{random.randint(51,100)}" for _ in range(n)],
        'Cost': [random.randint(500,5000) for _ in range(n)]
    })

def generate_dim_insurance(n):
    return pd.DataFrame({
        'I_InsuranceID': range(1, n+1),
        'ProviderName': [fake.company() for _ in range(n)],
        'PolicyType': [random.choice(['Basic','Premium','Gold','Silver']) for _ in range(n)],
        'CoveragePercent': [random.randint(50,100) for _ in range(n)],
        'ContactNumber': [fake.phone_number() for _ in range(n)]
    })

def generate_dim_payment_method(n):
    return pd.DataFrame({
        'PM_PaymentMethodID': range(1, n+1),
        'Method': [random.choice(['Cash','Credit Card','Insurance','UPI']) for _ in range(n)],
        'Bank': [fake.company() for _ in range(n)],
        'AccountNumber': [fake.bothify(text='##########') for _ in range(n)],
        'TransactionFee': [random.randint(0,100) for _ in range(n)]
    })

def generate_dim_room(n):
    return pd.DataFrame({
        'R_RoomID': range(1, n+1),
        'RoomNumber': [random.randint(100,500) for _ in range(n)],
        'RoomType': [random.choice(['ICU','General','Private']) for _ in range(n)],
        'Floor': [random.randint(1,10) for _ in range(n)],
        'Capacity': [random.randint(1,5) for _ in range(n)],
        'AvailabilityStatus': [random.choice(['Available','Occupied','Maintenance']) for _ in range(n)]
    })

def generate_dim_equipment(n):
    return pd.DataFrame({
        'E_EquipmentID': range(1, n+1),
        'EquipmentName': [fake.word().title() for _ in range(n)],
        'Category': [random.choice(['Monitoring','Surgical','Support','Imaging']) for _ in range(n)],
        'PurchaseDate': [fake.date_between(start_date='-10y', end_date='today') for _ in range(n)],
        'Cost': [random.randint(1000,50000) for _ in range(n)],
        'MaintenanceSchedule': [random.choice(['Monthly','Quarterly','Yearly']) for _ in range(n)]
    })

def generate_dim_lab_technician(n):
    return pd.DataFrame({
        'T_TechnicianID': range(1, n+1),
        'TechnicianName': [fake.name() for _ in range(n)],
        'ExperienceYears': [random.randint(1,30) for _ in range(n)],
        'Shift': [random.choice(['Morning','Evening','Night']) for _ in range(n)],
        'Certification': [random.choice(['Basic','Advanced','Critical Care']) for _ in range(n)]
    })

def generate_dim_date(n):
    return pd.DataFrame({
        'D_DateID': range(1, n+1),
        'FullDate': [fake.date_between(start_date='-5y', end_date='today') for _ in range(n)],
        'Day': [random.randint(1,31) for _ in range(n)],
        'Month': [random.randint(1,12) for _ in range(n)],
        'Year': [random.randint(2018,2025) for _ in range(n)],
        'Weekday': [random.choice(['Mon','Tue','Wed','Thu','Fri','Sat','Sun']) for _ in range(n)]
    })

# -----------------------------
# Generate Fact_LabTests
# -----------------------------
def generate_fact_labtests(fact_records, dim_records):
    # Dimension tables
    Dim_Patient_df = generate_dim_patient(dim_records)
    Dim_Doctor_df = generate_dim_doctor(dim_records)
    Dim_Hospital_df = generate_dim_hospital(dim_records)
    Dim_Department_df = generate_dim_department(dim_records)
    Dim_LabTest_df = generate_dim_lab_test(dim_records)
    Dim_Insurance_df = generate_dim_insurance(dim_records)
    Dim_PaymentMethod_df = generate_dim_payment_method(dim_records)
    Dim_Room_df = generate_dim_room(dim_records)
    Dim_Equipment_df = generate_dim_equipment(dim_records)
    Dim_Technician_df = generate_dim_lab_technician(dim_records)
    Dim_Date_df = generate_dim_date(dim_records)

    # Random foreign keys
    patient_ids = np.random.randint(1, dim_records+1, fact_records)
    doctor_ids = np.random.randint(1, dim_records+1, fact_records)
    hospital_ids = np.random.randint(1, dim_records+1, fact_records)
    department_ids = np.random.randint(1, dim_records+1, fact_records)
    labtest_ids = np.random.randint(1, dim_records+1, fact_records)
    insurance_ids = np.random.randint(1, dim_records+1, fact_records)
    paymentmethod_ids = np.random.randint(1, dim_records+1, fact_records)
    room_ids = np.random.randint(1, dim_records+1, fact_records)
    equipment_ids = np.random.randint(1, dim_records+1, fact_records)
    technician_ids = np.random.randint(1, dim_records+1, fact_records)
    date_ids = np.random.randint(1, dim_records+1, fact_records)

    # Fact table
    fact = pd.DataFrame({
        'LabTestVisitID': range(1, fact_records+1),
        'P_PatientID': patient_ids,
        'D_DoctorID': doctor_ids,
        'H_HospitalID': hospital_ids,
        'Dept_DepartmentID': department_ids,
        'L_LabTestID': labtest_ids,
        'I_InsuranceID': insurance_ids,
        'PM_PaymentMethodID': paymentmethod_ids,
        'R_RoomID': room_ids,
        'E_EquipmentID': equipment_ids,
        'T_TechnicianID': technician_ids,
        'D_DateID': date_ids,
        'ResultValue': np.random.randint(1,200,fact_records),
        'ResultStatus': [random.choice(['Normal','High','Low']) for _ in range(fact_records)],
        'ProcessingTimeMinutes': np.random.randint(30,240,fact_records)
    })

    # Merge 4 attributes from each dimension
    fact = fact.merge(Dim_Patient_df[['P_PatientID','PatientName','Gender','DOB','City']], on='P_PatientID', how='left')
    fact = fact.merge(Dim_Doctor_df[['D_DoctorID','DoctorName','Specialty','ExperienceYears']], on='D_DoctorID', how='left')
    fact = fact.merge(Dim_Hospital_df[['H_HospitalID','HospitalName','City','State']], on='H_HospitalID', how='left')
    fact = fact.merge(Dim_Department_df[['Dept_DepartmentID','DepartmentName','Floor','HeadDoctorID']], on='Dept_DepartmentID', how='left')
    fact = fact.merge(Dim_LabTest_df[['L_LabTestID','TestName','Category','NormalRange','Cost']], on='L_LabTestID', how='left')
    fact = fact.merge(Dim_Insurance_df[['I_InsuranceID','ProviderName','PolicyType','CoveragePercent']], on='I_InsuranceID', how='left')
    fact = fact.merge(Dim_PaymentMethod_df[['PM_PaymentMethodID','Method','Bank','AccountNumber']], on='PM_PaymentMethodID', how='left')
    fact = fact.merge(Dim_Room_df[['R_RoomID','RoomNumber','RoomType','Floor']], on='R_RoomID', how='left')
    fact = fact.merge(Dim_Equipment_df[['E_EquipmentID','EquipmentName','Category','Cost']], on='E_EquipmentID', how='left')
    fact = fact.merge(Dim_Technician_df[['T_TechnicianID','TechnicianName','ExperienceYears','Shift']], on='T_TechnicianID', how='left')
    fact = fact.merge(Dim_Date_df[['D_DateID','FullDate','Day','Month']], on='D_DateID', how='left')

    return fact

# -----------------------------
# Generate and save CSV
# -----------------------------
Fact_LabTests = generate_fact_labtests(num_records_fact, num_records_dim)
Fact_LabTests.to_csv('Fact_LabTests_Denormalized.csv', index=False)

print("Fact_LabTests CSV generated with 500,000 records, 10 IDs with 4 attributes each!")
