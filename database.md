# database.py
from sqlalchemy import create_engine, Column, Integer, String, Date, Time, Text
from sqlalchemy.orm import declarative_base, sessionmaker

DATABASE_URL = "postgresql://user:password@localhost/crm_db"

engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

class HCPInteraction(Base):
    __tablename__ = "hcp_interactions"

    id = Column(Integer, primary_key=True, index=True)
    hcp_name = Column(String(255), index=True)
    interaction_type = Column(String(100))
    date = Column(String(50))
    time = Column(String(50))
    attendees = Column(Text)
    topics_discussed = Column(Text)
    sentiment = Column(String(50))
    outcomes = Column(Text)
    follow_up_actions = Column(Text)

# Create tables
Base.metadata.create_all(bind=engine)